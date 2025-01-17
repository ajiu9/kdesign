---
order: 8
title: 异步加载
---

数据量较大时使用异步加载，需要在数据中传入 isLeaf 标明叶子节点

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import { Tree } from '@kdcloudjs/kdesign'

function Demo() {
  const data = [
    {
      key: '0',
      title: '0',
    },
    {
      key: '1',
      title: '1',
    },
    {
      key: '2',
      title: '2',
      isLeaf: true,
    },
  ]

  const [treeData, setTreeData] = React.useState(data)
  const [expandedKeys, setExpandedKeys] = React.useState([])

  const onExpand = (expandedKeys) => {
    console.log('onExpand', expandedKeys)
    setExpandedKeys(expandedKeys)
  }

  const updateTreeData = (list, key, children) => {
    return list.map((node) => {
      if (node.key === key) {
        return { ...node, children }
      }
      if (node.children) {
        return { ...node, children: updateTreeData(node.children, key, children) }
      }
      return node
    })
  }

  const onLoadData = ({ key, children }) => {
    return new Promise((resolve) => {
      if (children) {
        resolve()
        return
      }
      setTimeout(() => {
        setTreeData((origin) =>
          updateTreeData(origin, key, [
            {
              title: `${key}-0`,
              key: `${key}-0`,
            },
            {
              title: `${key}-1`,
              key: `${key}-1`,
            },
          ]),
        )
        resolve()
      }, 1000)
    })
  }

  return (
    <div style={{ width: '300px', height: '300px' }}>
      <Tree
        onExpand={onExpand}
        expandedKeys={expandedKeys}
        treeData={treeData}
        virtual={false}
        expandOnClickNode={false}
        loadData={onLoadData}
      />
    </div>
  )
}

ReactDOM.render(<Demo />, mountNode)
```

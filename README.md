<img alt="react-hook-data logo" src="https://raw.githubusercontent.com/betagouv/react-hook-data/master/icon.png" height=60/>

A lib for fetching normalized data in components via react hooks.

See the full [documentation](https://react-hook-data.netlify.com) for further complex use cases with several collections of data.

[![CircleCI](https://circleci.com/gh/betagouv/react-hook-data/tree/master.svg?style=svg)](https://circleci.com/gh/betagouv/react-hook-data/tree/master)
[![npm version](https://img.shields.io/npm/v/react-hook-data.svg?style=flat-square)](https://npmjs.org/package/react-hook-data)

## Basic Usage

Define your components as children of the `DataContext.Provider`:

```javascript
import { DataContext } from 'react-hook-data'

import Foos from './foos'

const Root = () => (
  <DataContext.Provider config={{ rootUrl: "https://momarx.com" }}>
    <Foos />
  </DataContext.Provider>
)

export default Root
```

Where Foos will access to the data elements:

```javascript
import React, { Fragment, useState } from 'react'
import { DataContext } from 'react-hook-data'

const Foos = () => {
  const { data, dispatch } = useContext(DataContext)
  const { foos } = data || {}
  const [error, setError] = useState(null)

  dispatch(requestData({
    apiPath: '/foos',
    handleFail: (state, action) => setError(action.error)
  }))

  if (error) {
    return error
  }

  return (
    <Fragment>
      {(foos || []).map(foo => (
        <div className="foo" key={foo.id}>
          {foo.text}
        </div>
      ))}
    </Fragment>
  )
}
```

## Usage with withData

Just rewrite your foos component as a container-like one:
```javascript
  const Foos = ({ foos }) => {
    const { dispatch } = useContext(DataContext)
    ...
  }
```

And use the `withData` hoc in order to make it more react-redux-like:

```javascript
import { withData } from 'react-hook-data'

import Foos from './foos'

const mapDataToProps(data, ownProps) {
  return {
    foos: (data.foos || []).filter(foo => foo.type === ownProps.type)
  }
}

const FoosContainer = withData(mapDataToProps)(Foos)
```

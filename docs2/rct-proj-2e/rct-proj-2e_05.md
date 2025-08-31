# 第五章：*第五章*：使用 Context 和 Hooks 构建个人购物清单应用程序

状态管理是现代 Web 和移动应用程序的重要组成部分，React 在此方面表现优秀。在 React 应用程序中处理状态管理可能会相当复杂，因为您有多种方式可以处理应用程序的当前状态。本书的前四章中创建的项目并没有过多关注状态管理，而这一点将在本章中深入探讨。

本章将展示您如何通过创建一个可从每个组件访问的应用程序状态来在 React 中处理状态管理。在 React v16.3 之前，您需要第三方包来处理 React 中的状态，但有了上下文 API 的更新版本，这不再是强制性的。此外，随着 React Hooks 的发布，引入了更多修改上下文的方法。通过一个示例应用程序，演示了处理应用程序状态管理的方法。

本章将涵盖以下主题：

+   使用上下文 API 进行状态管理

+   使用 Hooks 修改 Context

+   使用 React Suspense 进行代码拆分

# 项目概述

在本章中，我们将通过使用 Context 和 Hooks 来添加状态管理，构建一个个人购物清单。我们将基于使用 Create React App 创建的样板应用程序进行扩展，该应用程序使用 `react-router` 进行声明式路由。此外，还添加了 React Suspense 的代码拆分功能。

构建时间为 2.5 小时。

# 入门

本章中我们将创建的项目基于您可以在 GitHub 上找到的初始版本：[`github.com/PacktPublishing/React-Projects-Second-Edition/tree/main/Chapter05-initial`](https://github.com/PacktPublishing/React-Projects-Second-Edition/tree/main/Chapter05-initial)。完整的源代码也可以在 GitHub 上找到：[`github.com/PacktPublishing/React-Projects-Second-Edition/tree/main/Chapter05`](https://github.com/PacktPublishing/React-Projects-Second-Edition/tree/main/Chapter05)。

下载初始应用程序后，请确保您从项目的根目录运行 `npm install`。该项目使用 Create React App 创建，并安装了 `react`、`react-dom`、`react-scripts`、`styled-components` 和 `react-router-dom` 包，这些包您在之前的章节中已经见过。安装过程完成后，您可以从终端的同一选项卡中运行 `npm start`，并在浏览器中查看项目（`http://localhost:3000`）。

本节初始应用程序使用 Create React App 创建，并已实现路由和数据获取。当您打开应用程序时，一个显示标题、导航栏和两个列表的屏幕正在渲染。例如，如果您点击此处显示的第一个列表，将打开一个新页面，显示该列表的项目。在此页面上，您可以在右上角点击 **添加项目** 按钮打开一个新页面，该页面有一个用于添加新列表的表单，看起来如下：

![图 5.1 – 初始应用程序]

](img/Figure_5.1_B17390.jpg)

图 5.1 – 初始应用程序

此表单由 `ListForm` 组件渲染，目前没有功能，因为您将在稍后添加此功能。当您点击左侧按钮时，它将使用 `react-router-dom` 中的 navigate 方法重定向到之前访问的页面。

注意

当您尝试提交表单以添加新列表或向列表添加新项目时，目前没有任何反应。这些表单的功能将在本节稍后添加，您将使用 Context API 和 React Hooks 来实现。

该项目结构与您之前创建的应用程序相同。然而，在 `components` 目录中的可重用函数组件和在 `pages` 目录中表示路由的组件之间有所区分。页面组件使用了我们在 *第三章* 中见过的 `useDataFetching` 钩子，即 *构建动态项目管理板*，它增加了数据获取功能。

以下是对项目完整结构的概述：

```js
chapter-5-initial
  |- /node_modules
  |- /public
  |- /src
    |- /components
       |- /Button
          |- Button.js
       |- /FormItem
          |- FormItem.js
       |- /Header
          |- Header.js
       |- /NavBar
          |- NavBar.js
       |- /ListItem
          |- ListItem.js
    |- /hooks
       |- useDataFetching.js
    |- /pages
       |- ListDetail.js
       |- ListForm.js
       |- Lists.js
    |- App.js
    |- index.js
  package.json
```

该应用程序的入口点是 `src/index.js` 文件，它渲染 `App` 组件，该组件使用 `react-router-dom` 中的 `Router` 组件设置路由。`App` 组件包含一个 `Header` 组件和一个定义了四个路由的 `Switch` 路由组件。这些路由如下：

+   `/`: 渲染 `Lists` 组件，显示所有列表的概述

+   `/list/:listId`: 渲染 `ListDetail` 组件，显示特定列表中所有项目的概述

+   `/list/:listId/new`: 渲染 `ListForm` 组件，包含用于向特定列表添加新项目的表单

数据是从一个使用免费服务 My JSON Server 创建的模拟服务器中获取的，该服务从您项目根目录中的 `db.json` 文件创建服务器。该文件由一个具有两个字段 `items` 和 `lists` 的 JSON 对象组成，在模拟服务器上创建了多个端点。您在本章中将使用以下端点：

+   [`my-json-server.typicode.com/PacktPublishing/React-Projects-Second-Edition/items`](https://my-json-server.typicode.com/PacktPublishing/React-Projects-Second-Edition/items)

+   [`my-json-server.typicode.com/PacktPublishing/React-Projects-Second-Edition/lists`](https://my-json-server.typicode.com/PacktPublishing/React-Projects-Second-Edition/lists)

    注意

    `db.json` 文件必须存在于你的 GitHub 仓库的 master 分支（或默认分支）中，以便 My JSON Server 能够正常工作。否则，当你尝试请求 API 端点时，你会收到一个 **404 Not Found** 错误信息。

# 个人购物清单

在本节中，你将构建一个使用 Context 和 Hooks 进行状态管理的个人购物清单应用程序。使用这个应用程序，你可以创建可以添加项目、数量和价格的购物清单。本节的起点是一个已经启用了路由和本地状态管理的初始应用程序。

## 使用 Context API 进行状态管理

状态管理非常重要，因为应用程序的当前状态包含对用户有价值的数据。在之前的章节中，你已经通过使用 `useState` 和 `useEffect` 钩子来使用本地状态管理。当状态中的数据只对设置状态的组件重要时，这种模式非常有用。因为通过多个组件向下传递状态可能会变得混乱，你需要一种方法来访问你的应用程序中的 props，即使你并没有明确地将它们作为 props 传递。为此，你可以使用 React 的 Context API，这个 API 也被你在之前章节中使用的包如 `styled-components` 和 `react-router-dom` 所使用。

要在多个组件之间共享状态，我们将从本节的第一个部分开始探索 React 的一个功能，称为 Context。

## 创建 Context

当你想给你的 React 应用程序添加上下文时，你可以通过使用 React 的 `createContext` 方法创建一个新的上下文来实现。这会创建一个包含两个 React 组件的上下文对象，称为 Provider 和 Consumer。Provider 是放置上下文初始（以及随后的当前）值的地方，这些值可以通过 Consumer 内部的组件访问。

这是在 `src/App.js` 中的 `App` 组件中完成的，因为你希望列表的 Context 在由 `Route` 渲染的每个组件中都是可用的：

1.  让我们先为列表创建一个 Context，并使其可导出，以便列表数据可以在任何地方使用。为此，你可以在一个新目录 `src/context` 中创建一个名为 `ListsContext.js` 的新文件。在这个文件中，你可以添加以下代码：

    ```js
    import { createContext } from 'react';
    import useDataFetching from 
      '../hooks/useDataFetching';
    export const ListsContext = createContext();
    export const ListsContextProvider = ({ children }) => {
      const [loading, error, data] = useDataFetching(
        'https://my-json-server.typicode.com/
         PacktPublishing/React-Projects-Second-Edition/
         lists',
      );

      return (
        <ListsContext.Provider value=
          {{ lists: data, loading, error }}>
          {children}
        </ListsContext.Provider>
      );
    };
    export default ListsContext;
    ```

之前的代码创建了一个基于传递为 prop 的 `Context` 对象的 Provider，并根据从 `useDataFetching` 钩子返回的值设置了一个值，该钩子正在获取所有列表。使用 `children` prop，所有将被包裹在 `ListsContextProvider` 组件内部的组件都可以从 Consumer 中检索值的 data。

1.  你可以将 `ListsContextProvider` 组件导入到你的 `App` 组件中，以便包裹所有路由的 Router 组件：

    ```js
    import styled, { createGlobalStyle } from 
        'styled-components';
    import { Route, Routes, BrowserRouter } from 
        'react-router-dom';
    + import { ListsContextProvider } from 
        './context/ListsContext';
      // ...
    function App() { 
        return (
          <>
           <GlobalStyle />
           <AppWrapper>
             <BrowserRouter>
               <Header />
    +          <ListsContextProvider>
                 <Routes>
                   // ...
                 </Routes>
    +          </ListsContextProvider>
             </BrowserRouter>
           </AppWrapper>
         </>
       );
      }
      export default App;
    ```

1.  这样，你现在可以消费 `ListsContext` 提供者的值，从所有被 `ListsContextProvider` 包裹的组件中。在 `Lists` 组件中，可以使用 React 的 `useContext` Hook 通过传递 `ListsContext` 对象来检索这些数据。然后可以使用这些数据来渲染列表，并且可以从 `src/pages/Lists.js` 中移除 `useDataFetching` Hook：

    ```js
    + import { useContext } from 'react';
      import styled from 'styled-components';
      import { Link, useNavigate } from 
        'react-router-dom';
    - import useDataFetching from
        '../hooks/useDataFetching';
      import NavBar from '../components/NavBar/NavBar';
    + import ListsContext from '../context/ListsContext';
      // ...
      const Lists = () => {
        let navigate = useNavigate();
    -   const [loading, error, data] =
          useDataFetching('https://my-json-server.
          typicode.com/PacktPublishing/React-Projects-
          Second-Edition/lists');
    +   const { loading, error, lists } = 
          useContext(ListsContext);
      return (
              <>
            {navigate && <NavBar title='Your Lists' />}
            <ListWrapper>
              {loading || error ? (
                <span>{error || 'Loading...'}</span>
              ) : (
    -           data.map((list) => (
    +           lists.map((list) => (
                  <ListLink key={list.id} 
                   to={`list/${list.id}`}>
                    // ...
    ```

现在你已经从 `Lists` 中移除了 `useDataFetching` Hook，这个组件不再直接向 API 发送请求。列表的数据现在是从 `ListsContextProvider` 中获取的，并通过 `ListsContext` 传递，该上下文被 `Lists` 中的 `useContext` Hook 使用。如果你通过访问 `http://localhost:3000/` 在浏览器中打开应用程序，你可以看到列表的渲染与之前一样。

在下一节中，你还将为项目添加一个 Context 对象，这样项目也可以在 `react-router` 的 `Routes` 组件内的所有组件中使用。

## 嵌套上下文

就像列表数据一样，项目数据也可以存储在 Context 中并传递给需要这些数据的组件。这样，数据就不再是从任何渲染的组件中获取，而是只从 Context 中获取：

1.  再次，首先创建一个新文件，其中创建了一个上下文和一个提供者。这次，它被命名为 `ItemsContext.js`，也可以添加到 `src/context` 目录中：

    ```js
    import { createContext } from 'react';
    import useDataFetching from 
      '../hooks/useDataFetching';
    export const ItemsContext = createContext();
    export const ItemsContextProvider = ({ children }) => {
      const [loading, error, data] = useDataFetching(
        'https://my-json-server.typicode.com/
         PacktPublishing/React-Projects-Second-
         Edition/items',
      );
      return (
        <ItemsContext.Provider value=
          {{ items: data, loading, error }}>
          {children}
        </ItemsContext.Provider>
      );
    };
    export default ItemsContext;
    ```

1.  接下来，在 `src/App.js` 中导入这个新的 Context 和 Provider，你可以在 `ListsContextProvider` 组件下面嵌套它：

    ```js
      // ...
      import { ListsContextProvider } from 
        './context/ListsContext';
    + import { ItemsContextProvider } from 
        './context/ItemsContext';
      // ...

      function App() { 
        return (
          <>
            <GlobalStyle />
            <AppWrapper>
              <BrowserRouter>
                <Header />
                <ListsContextProvider>
    +             <ItemsContextProvider>
                    <Routes>
                      // ...
                    </Routes>
    +             </ItemsContextProvider>
                </ListsContextProvider>
              </BrowserRouter>
            </AppWrapper>
          </>
        );
      }
      export default App;
    ```

1.  `ListDetail` 组件现在可以从 `ItemsContext` 中获取项目，这意味着我们不再需要在组件中使用 `useDataFetching` Hook。为了实现这一点，你需要对 `src/pages/ListDetail.js` 进行以下更改：

    ```js
    - import { useState, useEffect } from 'react';
    + import { useState, useEffect, useContext } from 
        'react';
      import styled from 'styled-components';
      import { useNavigate, useParams } from 
        'react-router-dom';
    - import useDataFetching from 
        '../hooks/useDataFetching';
      import NavBar from '../components/NavBar/NavBar';
      import ListItem from
        '../components/ListItem/ListItem';
    + import ItemsContext from '../context/ItemsContext';
      // ...
      function ListDetail() {
        let navigate = useNavigate();
        const { listId } = useParams();
    -   const [loading, error, data] =
          useDataFetching('https://my-json-server.
          typicode.com/PacktPublishing/React-Projects-
          Second-Edition/items/');
    +   const { loading, error, items: data } = 
          useContext(ItemsContext);
        // ...
    ```

现在所有数据获取都不是由 `List` 和 `Lists` 组件完成的。通过嵌套这些 Context 提供者，返回值可以被多个组件消费。但这还不是最佳方案，因为你现在在启动应用程序时加载了所有列表和所有项目。

这种方法的缺点是，一旦我们打开列表的详细页面，它将检索所有项目，即使它们不是属于这个列表的。在下一节中，你将看到如何通过结合 Context 和自定义 Hooks 来获取所需的数据。

# 使用 Hooks 修改上下文

你可以从 Context 中有条件地获取数据的方式有很多；其中之一是将 Context 中的数据放在本地状态中。这可能是一个适用于较小应用程序的解决方案，但对于较大的应用程序来说可能效率不高，因为你仍然需要将这个状态传递到组件树中。另一个解决方案是使用 React Hooks 创建一个函数，并将其添加到 Context 的值中，可以从嵌套在这个 Context 中的任何组件中调用。此外，这种方法获取数据可以让你高效地只加载所需的数据。

如何将 React 生命周期和状态管理结合使用 Hooks 进行演示，在本节的第一部分。

## 在函数组件中使用生命周期

之前，我们使用`useDataFetching`钩子来帮我们进行数据获取，但这并不让我们能够精确控制数据何时被获取。从消耗我们上下文数据的组件中，我们希望能够启动数据获取。因此，我们需要为它们添加生命周期，这些生命周期会在我们的上下文组件内部调用一个函数来执行数据获取。按照以下步骤来实现这一功能：

1.  实现这一功能的第一步是在`src/context/ItemsContext.js`文件中添加进行数据获取的逻辑。这个逻辑将替换掉`useDataFetching`钩子的使用，从添加用于数据获取状态的本地区域变量开始：

    ```js
    - import { createContext } from 'react';
    - import useDataFetching from 
        '../hooks/useDataFetching';
    + import { createContext, useCallback, useState } from
        'react';
      export const ItemsContext = createContext();
      export const ItemsContextProvider = ({ children }) 
        => {
    -   const [loading, error, data] =
          useDataFetching('https://my-json-server.
          typicode.com/PacktPublishing/React-Projects-
          Second-Edition/items);
    +   const [loading, setLoading] = useState(true);
    +   const [items, setItems] = useState([]);
    +   const [error, setError] = useState('');
        // ...
    ```

1.  在此之后，我们可以添加一个名为`fetchItems`的函数，并将其传递给`ItemsContextProvider`，这意味着它将被添加到上下文中。这个函数被`useCallback`钩子包装，以防止组件不必要的（重新）渲染：

    ```js
        // ...
        const [error, setError] = useState('');
    +   const fetchItems = useCallback(async (listId) => {
    +     try {
    +       const data = await fetch(`https://my-json-
              server.typicode.com/PacktPublishing/
              React-Projects-Second-Edition/lists/
              ${listId}/items`);
    +       const result = await data.json();
    +       if (result) {
    +         setItems(result);
    +         setLoading(false);
    +       }
    +     } catch (e) {
    +       setLoading(false);
    +       setError(e.message);
    +     }
    +   }, [])
        return (
    -     <ItemsContext.Provider value={{ data: items,
            loading, error }}>
    +     <ItemsContext.Provider value={{ items, loading,
            error, fetchItems }}>
          // ...
    ```

1.  在这个函数就位后，下一步就是从`ListDetail`组件中调用它，并传递一个`listId`的值。这意味着一旦加载了这个组件，我们就不再检索所有项目，而是使用 URL 中的参数来确定应该获取并添加到上下文中的数据：

    ```js
    - import { useState, useEffect, useContext } from
       'react';
    + import { useEffect, useContext } from 'react';
      import styled from 'styled-components';
      // ...
      function ListDetail() {
        let navigate = useNavigate();
        const { listId } = useParams();
    -   const { loading, error, items: data } =
          useContext(ItemsContext);
    +   const { loading, error, items, fetchItems } = 
          useContext(ItemsContext);
    -   const [items, setItems] = useState([]);
    -   useEffect(() => {
    -     data && listId && setItems(data.filter((item) =>
            item.listId === parseInt(listId)));
    -   }, [data, listId]);
    +   useEffect(() => {
    +     listId && !items.length && fetchItems(listId);
    +   }, [fetchItems, items, listId]);
        return (
          // ...
    ```

之前的`useEffect`钩子会在页面 URL 中存在`listId`并且`items`的值为空数组时调用`fetchItems`函数。这防止我们在`ItemsContext`中已经存在项目时再次获取项目。

通过在我们的上下文中创建一个进行数据获取的函数，我们现在可以控制数据何时被获取，因此将不再有对 API 的不必要请求。但其他钩子也可以直接将数据传递给 Provider，而无需重复`useState`钩子。这将在本节的下一部分进行演示。

## 使用`useReducer`进行高级状态管理

另一种向 Provider 添加数据的方法是使用类似于 Facebook 引入的 Flux 的模式。Flux 模式描述了一种数据流，其中正在分发动作以从存储中检索数据并将其返回给视图。这意味着动作需要在某处进行描述；应该有一个中央位置来存储数据，并且视图可以读取这些数据。为了使用上下文 API 实现此模式，你可以使用另一个名为`useReducer`的钩子。这个钩子可以用来返回数据，而不是从本地状态，而是从任何数据变量：

1.  正如与`useState`钩子一样，`useReducer`钩子需要添加到使用它的组件中。`useReducer`将接受一个初始状态和一个函数，该函数确定应该返回哪些数据。这个初始值需要在添加钩子之前添加到`src/context/ListsContext.js`文件中：

    ```js
    - import { createContext } from 'react';
    + import { createContext, useCallback, useReducer }
        from 'react';
      const ListsContext = createContext();
    + const initialState = {
    +   lists: [],
    +   loading: true,
    +   error: '',
    + };
      // ...
    ```

1.  除了初始值之外，`useReducer` Hook 还接受一个名为`reducer`的函数。这个`reducer`函数也应该被创建，它是一个根据发送给它的操作更新`initialState`（已传递）并返回当前值的函数。如果发送给 reducer 的操作不匹配`reducer`中定义的任何操作，reducer 将只返回当前值而不做任何更改：

    ```js
      import { createContext, useReducer } from 'react';
      const ListsContext = createContext();
      // ...
    + const reducer = (state, action) => {
    +   switch (action.type) {
    +     case 'GET_LISTS_SUCCESS':
    +       return {
    +         ...state,
    +         lists: action.payload,
    +         loading: false,
    +       };
    +     case 'GET_LISTS_ERROR':
    +       return {
    +         ...state,
    +         lists: [],
    +         loading: false,
    +         error: action.payload,
    +       };
    +     default:
    +       return state;
    +   }
    + };

      export const ListsContextProvider = ({ children }) => {
        // ...
    ```

1.  `useReducer` Hook 的两个参数现在已添加到文件中，因此你需要添加实际的 Hook 并将`initialState`和`reducer`传递给它。`useDataFetching` Hook 可以被移除，因为这将被一个新的具有数据获取逻辑的函数所取代：

    ```js
      // ...
    const ListsContextProvider = ({ children }) => { 
    -   const [loading, error, data] =
          useDataFetching('https://my-json-server.
          typicode.com/PacktPublishing/React-Projects-
          Second-Edition/lists');
    +   const [state, dispatch] = 
          useReducer(reducer, initialState);
      // ...
    ```

1.  如你所见，`reducer`在接收到`GET_LISTS_SUCCESS`或`GET_LISTS_ERROR`操作时改变了它返回的值。在之前提到之前，你可以通过使用由`useReducer` Hook 返回的`dispatch`函数来调用这个`reducer`。然而，因为你还要处理数据的异步获取，你不能直接调用这个函数。相反，你需要创建一个调用`fetchData`函数并在之后发送正确操作的`async/await`函数：

    ```js
      // ...
      export const ListsContextProvider = ({ children })
        => {
        const [state, dispatch] = 
          useReducer(reducer, initialState);
    +   const fetchLists = useCallback(async () => {
    +     try {
    +       const data = await fetch(`https://my-json-
              server.typicode.com/PacktPublishing/React-
              Projects-Second-Edition/lists`);
    +       const result = await data.json();
    +       if (result) {
    +         dispatch({ type: 'GET_LISTS_SUCCESS', 
                         payload: result });
    +       }
    +     } catch (e) {
    +       dispatch({ type: 'GET_LISTS_ERROR', 
              payload: e.message });
    +     }
    +   }, [])
      return (
        // ...
    ```

1.  在前面的`fetchLists`函数中，它调用 API，如果有结果，将使用来自`useReducer` Hook 的`dispatch`函数将`GET_LISTS_SUCCESS`操作发送到 reducer。如果没有，将发送`GET_LISTS_ERROR`操作，它返回一个错误消息。

1.  必须将状态和`fetchLists`函数的值添加到 Provider 中，这样我们就可以从其他组件通过 Context 访问它们：

    ```js
        // ...
        return (
    -     <ListsContext.Provider value=
            {{ loading, error, data: lists }}>
    +     <ListsContext.Provider value=
            {{ ...state, fetchLists }}>
            {children}
          </ListsContext.Provider>
        );
      };
      export default ListsContext;
    ```

1.  现在，这个`getLists`函数可以从显示列表的组件中的`useEffect` Hook 调用，即`Lists`组件，在第一次渲染时。列表应该在还没有任何列表可用时才检索：

    ```js
    - import { useContext } from 'react';
    + import { useContext, useEffect } from 'react';
      import styled from 'styled-components';
      import { Link, useNavigate } from 
        'react-router-dom';
      import NavBar from '../components/NavBar/NavBar';
      import ListsContext from '../context/ListsContext';
      // ...
      function Lists() {
        let navigate = useNavigate();

    -   const { loading, error, lists } =
          useContext(ListsContext);
    +   const { loading, error, lists, fetchLists } = 
          useContext(ListsContext);
    +   useEffect(() => {
    +     !lists.length && fetchLists()
    +   }, [fetchLists, lists])
        return (
          // ...
    ```

如果你现在再次在浏览器中访问项目，你可以看到列表的数据就像之前一样被加载。最大的不同是数据是使用 Flux 模式获取的，这意味着这可以扩展到在其他实例中获取数据。同样，对于`ItemsContext`也可以在`src/context/ItemsContext.js`文件中这样做：

1.  首先，导入`useReducer` Hook，并为项目添加初始值和我们将与这个 Hook 一起使用的`reducer`函数：

    ```js
    - import { createContext, useState } from 'react';
    + import { createContext, useReducer } from 'react';
      export const ItemsContext = createContext();
    + const initialState = {
    +   items: [],
    +   loading: true,
    +   error: '',
    + };
    + const reducer = (state, action) => {
    +   switch (action.type) {
    +     case 'GET_ITEMS_SUCCESS':
    +       return {
    +         ...state,
    +         items: action.payload,
    +         loading: false,
    +       };
    +     case 'GET_ITEMS_ERROR':
    +       return {
    +         ...state,
    +         items: [],
    +         loading: false,
    +         error: action.payload,
    +       };
    +     default:
    +       return state;
    +   }
    + };
      export const ItemsContextProvider = 
        ({ children }) => {
        // ...
    ```

1.  在此之后，你可以将初始状态和 reducer 添加到`useReducer` Hook 中。该文件中已经存在的`fetchItems`函数必须进行更改，以便它将使用来自`useReducer`的`dispatch`函数，而不是来自`useState` Hook 的`update`函数：

    ```js
      // ...
      export const ItemsContextProvider =
        ({ children }) => {
    -   const [loading, setLoading] = useState(true);
    -   const [items, setItems] = useState([]);
    -   const [error, setError] = useState('');
    +   const [state, dispatch] =
          useReducer(reducer, initialState);
      const fetchItems = useCallback(async (listId) => {
        try {
          const data = await fetch(`https://my-json-
            server.typicode.com/PacktPublishing/React-
            Projects-Second-Edition/lists/${listId}/
            items`);
          const result = await data.json();
          if (result) {
    -       setItems(result);
    -       setLoading(false);
    +       dispatch({ type: 'GET_ITEMS_SUCCESS', 
               payload: result });
          }
        } catch (e) {
    -     setLoading(false);
    -     setError(e.message);
    +     dispatch({ type: 'GET_ITEMS_ERROR', 
            payload: e.message });
        }
      }, [])
      return (
        // ...
    ```

1.  此外，将状态和`fetchItems`函数添加到`ListsContextProvider`：

    ```js
        // ...
        return (
    -     <ItemsContext.Provider value={{ items, loading, 
            error, fetchItems }}>
    +     <ItemsContext.Provider value=
            {{ ...state, fetchItems }}>
            {children}
          </ItemsContext.Provider>
        );
      };
      export default ItemsContext;
    ```

如果你现在打开特定的列表，例如`/lists/:listId`路由上的列表，例如`http://localhost:3000/list/1`，你会看到没有任何变化，列表的项目仍然被显示。

你可能会注意到列表的标题在这里没有显示。列表的信息只有在`Lists`组件首次渲染时才会被检索，所以你需要创建一个新的函数来始终检索你目前在`List`组件中显示的列表信息：

1.  在`src/context/ListsContextProvider.js`文件中，你需要扩展`initialState`以包含一个名为`list`的字段：

    ```js
      import { createContext, useReducer } from 'react';
      export const ListsContext = createContext();
      const initialState = {
        lists: [],
    +   list: {},
        loading: true,
        error: '',
      };
      const reducer = (state, action) => {
        // ...
    ```

1.  在`reducer`中，你现在还必须检查两个新的动作，这两个动作要么将关于列表的数据添加到上下文中，要么添加一个错误消息：

    ```js
      // ...
      const reducer = (state, action) => {
        switch (action.type) {
          case 'GET_LISTS_SUCCESS':
             // ...
          case 'GET_LISTS_ERROR':
            // ...
    +     case 'GET_LIST_SUCCESS':
    +       return {
    +         ...state,
    +         list: action.payload,
    +         loading: false,
    +       };
    +     case 'GET_LIST_ERROR':
    +       return {
    +         ...state,
    +         list: {},
    +         loading: false,
    +         error: action.payload,
    +       };
          default:
            return state;
        }
      };
      export const ListsContextProvider = 
        ({ children }) => {
        // ...
    ```

1.  这些动作将从一个新的`fetchList`函数中分发，该函数接受一个特定列表的 ID 来调用 API。如果成功，将分发`GET_LIST_SUCCESS`动作；否则，将分发`GET_LIST_ERROR`动作。同时，将函数传递给 Provider，以便它可以在其他组件中使用：

    ```js
      // ...
    +   const fetchList = useCallback(async (listId) => {
    +     try {
    +       const data = await fetch(`https://my-json-
              server.typicode.com/PacktPublishing/React-
              Projects-Second-Edition/lists/${listId}`);
    +       const result = await data.json();
    +       if (result) {
    +         dispatch({ type: 'GET_LIST_SUCCESS',
                payload: result });
    +       }
    +     } catch (e) {
    +       dispatch({ type: 'GET_LIST_ERROR', 
              payload: e.message });
    +     }
    +   }, [])
        return (
    -     <ListsContext.Provider value=
            {{ ...state, fetchLists }}>
    +     <ListsContext.Provider value=
            {{ ...state, fetchLists, fetchList }}>
            {children}
          </ListsContext.Provider>
        );
      };
      export default ListsContext;
    ```

1.  在`ListDetail`组件中，我们可以通过在`useEffect` Hook 中调用`fetchList`函数从`ListsContext`获取列表数据。同时，将其作为 prop 传递给`NavBar`组件，以便它将被显示：

    ```js
      import { useEffect, useCallback, useContext } from
        'react';
      import styled from 'styled-components';
      import { useNavigate, useParams } from 
        'react-router-dom';
      import NavBar from '../components/NavBar/NavBar';
      import ListItem from
        '../components/ListItem/ListItem';
      import ItemsContext from '../context/ItemsContext';
    + import ListsContext from '../context/ListsContext';
      // ...
      function ListDetail() {
        let navigate = useNavigate();
        const { listId } = useParams();
        const { loading, error, items, fetchItems } = 
          useContext(ItemsContext);
    +   const { list, fetchList } = 
          useContext(ListsContext);
        useEffect(() => {
          listId && !items.length && fetchItems(listId);
        }, [fetchItems, items, listId]);
    +   useEffect(() => {
    +     listId && fetchList(listId);
    +   }, [fetchList, listId]);
        return (
          <>
            {navigate && (
              <NavBar
                goBack={() => navigate(-1)}
                openForm={() =>
                  navigate(`/list/${listId}/new`)}
    +           title={list && list.title}
              />
            )}
            // ...
    ```

在前面的代码块中，我们是从一个与`fetchItems`函数不同的`useEffect` Hook 中调用`fetchList`函数。这是一种很好的分离关注点的方法，以保持我们的代码干净和简洁。

你应用中的所有数据现在都是通过 Provider 加载的，这意味着它现在与视图分离。此外，`useDataFetching` Hook 被完全移除，使你的应用程序结构更易于阅读。

你不仅可以使用这个上下文 API 与这个模式来使数据对许多组件可用，还可以修改数据。如何修改这些数据将在下一节中展示。

# 在 Provider 中修改数据

你不仅可以使用这种 Flux 模式检索数据，还可以用它来更新数据。模式保持不变：你分发一个动作，该动作会触发对服务器的请求，并根据结果，reducer 将使用这个结果来修改数据。根据是否成功，你可以显示一个成功消息或一个错误消息。

代码已经有一个用于向列表添加新项目的表单——但目前这个功能还没有正常工作。让我们创建一个机制来通过更新`items`的 Provider 来添加项目：

1.  第一步是在`ItemsContext`中添加新的动作，一旦我们尝试添加一个新项目，这个动作就会被分发：

    ```js
      // ... 
      const reducer = (state, action) => {
        switch (action.type) {
          // ...   
    +     case 'ADD_ITEM_SUCCESS':
    +       return {
    +         ...state,
    +         items: [...state.items, action.payload],
    +         loading: false,
    +       };
          default:
            return state;
        }
      };
      export const ItemsContextProvider = 
        ({ children }) => {
        // ...
    ```

1.  我们还需要添加一个新的函数来处理`POST`请求，因为这个函数在处理`fetch`请求时也应该设置方法和一个主体。你可以在前面的文件中创建这个函数，并将其传递给 Provider：

    ```js
      // ...  
    items, so this data is available from the Consumer.
    ```

1.  由于现在可以从提供者处获取添加新项目到列表的函数，`src/pages/ListForm.js`中的`ListForm`组件现在能够使用`addItem`函数，这将触发调用 API 并将项目添加到状态的行动。然而，表单中输入字段的值需要首先确定。因此，输入字段需要是受控组件，意味着它们的值由封装值的本地状态控制。因此我们需要导入`useState`钩子和一个`useContext`钩子，我们将在稍后使用它来从传递给它的上下文的提供者中获取值：

    ```js
    + import { useState, useContext } from 'react';
      import styled from 'styled-components';
      import { useNavigate, useParams } from 
       'react-router-dom';
      import NavBar from '../components/NavBar/NavBar';
      import FormItem from 
        '../components/FormItem/FormItem';
      import Button from '../components/Button/Button';
    + import ItemsContext from '../context/ItemsContext';
      // ...
      function ListForm() {
        let navigate = useNavigate();
        const { listId } = useParams();
    +   const [title, setTitle] = useState('');
    +   const [quantity, setQuantity] = useState('');
    +   const [price, setPrice] = useState('');
        return (
          // ...   
    ```

为了做到这一点，我们为每个创建的`state`值使用了`useState`钩子。

1.  本地状态值和触发本地状态值更新的函数必须作为属性设置在`FormItem`组件上：

    ```js
         // ...
         return (
           <>
             {navigate && <NavBar goBack={() => 
               navigate(-1)} title={`Add Item`} />}
             <FormWrapper>
               <form>
                 <FormItem
                   id='title'
                   label='Title'
                   placeholder='Insert title'
    +              value={title}
    +              handleOnChange={(e) =>
                     setTitle(e.currentTarget.value)}
                 />
                 <FormItem
                   id='quantity'
                   label='Quantity'
                   type='number'
                   placeholder='0'
    +              value={quantity}
    +              handleOnChange={(e) =>
                     setQuantity(e.currentTarget.value)}
                 />
                 <FormItem
                   id='price'
                   label='Price'
                   type='number'
                   placeholder='0.00'
    +              value={price}
    +              handleOnChange={(e) => 
                     setPrice(e.currentTarget.value)}
                 />
                 <SubmitButton>Add Item</SubmitButton>
               </form>
             </FormWrapper>
           </>
         );
      };
      export default Form;
    ```

1.  现在你需要做的最后一件事是添加一个函数，当点击提交按钮提交表单时将被触发。这个函数接受`value`作为本地状态，添加关于项的信息，并使用这个信息来调用`addItem`函数。在这个函数被调用后，`useNavigate`中的导航函数被调用，以便返回到该列表的概览页面：

    ```js
      // ...
    + const { addItem } = useContext(ItemsContext);
    + function onSubmit(e) {
    +   e.preventDefault();
    +   if (title && quantity && price) {
    +     addItem({
    +       title,
    +       quantity,
    +       price,
    +       listId,
    +     });
    +   }
    +   navigate(`/list/${listId}`);
    + }
      return (
        <>
          {navigate && <NavBar goBack={() =>
            navigate(-1)} title={`Add Item`} />}
          <FormWrapper>
    -       <form>
    +       <form onSubmit={onSubmit}>
              // ...
    ```

当你现在提交表单时，将向模拟服务器发送一个`POST`请求。你将被送回到上一个页面，在那里你可以看到结果。如果成功，将触发`GET_LIST_SUCCESS`操作，你插入的项目被添加到列表中。

到目前为止，来自上下文的信息仅通过使用提供者单独使用，但也可以将其组合成一个应用程序上下文，如下一节所示。

# 创建应用程序上下文

如果你查看`App`组件中当前的路由结构，你可以想象，如果你向应用程序中添加更多的提供者和消费者，这将会变得混乱。像 Redux 这样的状态管理包通常有一个应用程序状态，其中存储了应用程序的所有数据。当使用上下文时，可以创建一个应用程序上下文，可以使用`useContext`钩子访问它。这个钩子充当消费者，可以从传递给它的上下文的提供者中检索值。让我们重构当前的应用程序，使其具有应用程序上下文：

1.  首先，在`src/context`目录中创建一个名为`AppContext.js`的文件。这个文件将导入`ListsContextProvider`和`ItemsContextProvider`，将它们嵌套，并将任何将被传递给它的组件作为`children`属性包裹：

    ```js
    import { ListsContextProvider } from './ListsContext';
    import { ItemsContextProvider } from './ItemsContext';
    const AppContext = ({ children }) => {
      return (
        <ListsContextProvider>
          <ItemsContextProvider>{children}
          </ItemsContextProvider>
        </ListsContextProvider>
      );
    };
    export default AppContext;
    ```

1.  在`src/App.js`文件中，我们现在可以导入这个`AppContext`文件，以替代列表和项的提供者，并将`ListsContextProvider`和`ItemsContextProvider`替换为`AppContext`：

    ```js
      import styled, { createGlobalStyle } from 
        'styled-components';
      import { Route, Routes, BrowserRouter } from 
        'react-router-dom';
      import Header from './components/Header/Header';
      import Lists from './pages/Lists';
      import ListDetail from './pages/ListDetail';
      import ListForm from './pages/ListForm';
    - import { ListsContextProvider } from 
        './context/ListsContext';
    - import { ItemsContextProvider } from
        './context/ItemsContext';
    + import AppContext from './context/AppContext';
      // ...
      function App() { 
        return (
          <>
            <GlobalStyle />
            <AppWrapper>
              <BrowserRouter>
                <Header />
    +           <AppContext>
    -           <ListsContextProvider>
    -             <ItemsContextProvider>
                    <Routes>
                      // ...
                    </Routes>
    -             </ItemsContextProvider>
    -           </ListsContextProvider>
    +           </AppContext>
              </BrowserRouter>
            </AppWrapper>
          </>
        );
      }
      export default App;
    ```

`AppContext`组件可以扩展到所有你可能希望在将来添加的上下文对象。我们的应用程序现在有一个更干净的结构，而数据仍然由提供者检索。

# 使用 React Suspense 进行代码拆分

到目前为止，我们主要关注添加新功能，如路由或状态管理，到我们的应用程序中。但并没有太多关注于使我们的应用程序更高效，我们可以通过代码拆分来实现这一点。React 的一个名为 Suspense 的功能可以用于代码拆分，这意味着你将编译后的代码（你的包）拆分成更小的块。这将防止浏览器一次性下载包含编译代码的整个包，而是根据浏览器渲染的组件分块加载你的包。

注意

在上一章中，我们使用 Next.js 而不是 Create React App 来创建我们的 React 应用程序，它默认启用了代码拆分。

Suspense 允许你的组件等待直到你导入的组件准备好显示。在 React 18 之前，它只能用于代码拆分，但自从最新版本的 React 以来，它有更多的用途。当你从使用 Suspense 导入的组件获取数据时，React 也会等待直到该组件的数据完全获取。

悬念必须与懒加载方法结合使用，这涉及到使用 JavaScript 动态导入来仅在请求时加载组件。这两种方法都可以从 React 的`src/App.js`中导入，其中懒加载方法用于导入我们页面的组件：

```js
+ import { Suspense, lazy } from 'react';
  import styled, { createGlobalStyle } from 
    'styled-components';
  import { Route, Routes, BrowserRouter} from 
    'react-router-dom';
  import Header from './components/Header/Header';
- import Lists from './pages/Lists';
- import ListDetail from './pages/ListDetail';
- import ListForm from './pages/ListForm';
  import AppContext from './context/AppContext';
+ const Lists = lazy(() => import('./pages/Lists'));
+ const ListDetail = lazy(() =>
    import('./pages/ListDetail'));
+ const ListForm = lazy(() => import('./pages/ListForm'));
  // ...

  function App() {
    // ...
```

在`App`组件的`return`语句中，Suspense 必须与一个回退一起使用，当动态导入的组件正在加载时，将显示该回退：

```js
  // ...
  function App() {
    return (
      <>
        <GlobalStyle />
        <AppWrapper>
          <BrowserRouter>
            <Header />
+           <Suspense fallback={<div>Loading...</div>}>
              <AppContext>
                // ...
              </AppContext>
+           </Suspense>
          </BrowserRouter>
        </AppWrapper>
      </>
    );
  }
  export default App;
```

当你在浏览器中查看应用程序时，除非你有慢速的互联网连接，否则你不会看到任何变化。在这种情况下，Suspense 的回退将在组件加载时显示。然而，当你打开`bundle.js`和`main.chunk.js`时。然而，在应用代码拆分后，块组件也会被加载，例如，`src_pages_ListDetail_js.js`。

![图 5.2 – 代码拆分后我们应用程序的网络请求

![图 5.2_B17390.jpg]

图 5.2 – 代码拆分后我们应用程序的网络请求

查看主路由`/`，我们可以看到名为`3.chunk.js`的块被加载。这不是一个非常有用的文件名，我们可以通过在行内注释中使用`webpackChunkName`来更改它。通过这个添加，我们可以指导 webpack 将文件命名为更用户友好的名称：

```js
  // ...
- const Lists = lazy(() => import('./pages/Lists'));
- const ListDetail = lazy(() =>
    import'./pages/ListDetail'));
- const ListForm = lazy(() => import('./pages/ListForm'));
+ const Lists = lazy(() => import(/* webpackChunkName:
    "Lists" */ './pages/Lists'));
+ const ListDetail = lazy(() => import(/* webpackChunkName:
    "ListDetail" */ './pages/ListDetail'));
+ const ListForm = lazy(() => import(/* webpackChunkName: 
    "ListForm" */ './pages/ListForm'));
  function App() {
    // ...
```

这个最新的功能使得识别我们应用程序中加载了哪些块（或组件）变得容易得多，正如你通过在浏览器中重新加载应用程序并再次检查网络标签所看到的那样。

# 摘要

在本章中，您创建了一个使用 Context API 和 Hooks 传递和检索数据的购物清单应用程序。Context 用于存储数据，Hooks 用于检索和修改数据。使用 Context API，您可以使用 `useReducer` Hook 创建更高级的状态管理场景。Context 还用于创建一个所有数据都存储在应用程序范围内，并且可以通过创建共享 Context 从任何组件访问的情况。最后，我们使用了 React Suspense 来对我们的包应用代码拆分，以改善性能。

在下一章中也将使用 Context API，这将向您展示如何使用 Jest 和 Enzyme 等库进行自动化测试来构建一个酒店评论应用程序。它将向您介绍多种测试您使用 React 创建的 UI 组件的方法，并展示如何使用 Context API 测试您应用程序中的状态管理。

# 进一步阅读

更多信息，请参阅以下链接：

+   消费多个 Context 对象：[`reactjs.org/docs/context.html#consuming-multiple-contexts`](https://reactjs.org/docs/context.html#consuming-multiple-contexts)

+   React Suspense：[`reactjs.org/docs/react-api.html#reactsuspense`](https://reactjs.org/docs/react-api.html#reactsuspense)

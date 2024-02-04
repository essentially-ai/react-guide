# REACT GUIDE
> This document is the contribution guide for our react application.

> Treat this like a coding standard which you must follow at all times including but not limited to coding, debugging and submitting the PR.


#### I've divided the document into multiple parts based on the type of standards are listed in them.


## Functional Standards
> This contains all the specs related to user interaction with the application, just pure english and no fancy tech jargon.

1. **ALWAYS** ask for the reason for making anything, understand the full context before you start writing your first line of code. 
   1. You might come up with better solutions to the same problem.
   2. You'll be able to predict the next few extensions of the current feature, this will help you with structuring your code.

2. **ALWAYS** think about the next few extensions for the feature you're working on.
   1. Not thinking about this will make the product strongly coupled with the current version, this will make it extremely difficult to scale up. 
   2. Let it be in terms of extra functionality, changing the UI or using the component somewhere else.
    > Ex. If I'm working on the websocket module, I'd think about reconnections, failed attempts, subscriptions during connection failure etc...

    > Ex. If I'm working on RMS checks, orders page, I'd think about what if I want to add extra RMS checks. What if there are a lot of orders, how will my component pan out in that case.

3. User should **ALWAYS** get feedback from their interaction
   1. Be it from color change, notification or as simple as a loading screen.
   2. Start thinking from the user perspective and see how your feature pans out. If you will not use it, the user won't.
   3. User should not feel any lag between their interaction and the UI change.
    >Ex. If user clicks on place order, the blob should immediately close instead of waiting for a response from backend. This response can be shown as a new notification. 
    
    > Ex. When user tries to add an instrument into watchlist, it should get added immediately and then in case the API call fails, it should revert back.
   4. Use the feature in the exact flow which user is supposed to take. Then take a few de-tours (like changing pages and triggering the same functionality) to see if it works as expected.
   5. User should not be able to burst requests to the backend from UI.


## Technical Standards
> This contains all the specs related to the technical aspect of the application.

1. Always prioritize memory usage, never create copies inside components.
   1. If at all copies are required, they should be done inside the context of a function which should get immediately destroyed once the function is executed.
   2. Copies inside the redux reducer would be acceptable in case some complex mutation is required.
   3. In the extreme case that copy is required in the component, make sure to minimize the copy size to "just enough" to work with.

2. Avoid prop drilling as much as possible
   1. If a prop is passing down to more than 1 child, there's something seriously wrong with it.
   2. Either use redux store or use a context. In most of our cases, redux would do the job.
   3. If you don't see any other solution, please raise the issue so we discuss it further.

3. **ALWAYS** focus on minimizing rerenders
   1. Before submitting a PR, write the number of renders your feature is causing.
   2. Ideally it should be limited only to the component changing value. Worst case scenario, the parent component.
   3. If any component rooted at a different feature is getting rendered because of the change, there's something seriously wrong.

4. Redux conventions [Redux Style Guide](https://redux.js.org/style-guide/)
   1. Have a strong base on redux architecture.
   2. Reducer should not have any side effects, just change it's own slice and leave it there.
   3. Ask this question everytime you make a reducer "Is it taking the action and **ONLY** modifying it's own slice **ENTIRELY** based on the action?". If the answer is no, then the reducer needs to be changed.
   4. If side effects are needed in a different slice or anything extra, create a new reducer in the respective slice or do things from main codebase.
   5. Never interact with the state directly, these interactions should only be done by a reducer, inside a reducer.
   6. Always check the chrome console for warnings, aim to make it 0 warnings.

5. Follow the single responsibility principle, one function should only have a single job.
   1. In case you think multiple functions can be merged together, first make individual functions and make them work.
   2. Once they all work independent of each other, then start merging them into one; Provided the maintainability doesn't take a significant hit and that the independent small parts don't have to work standalone.
   3. In case where they have to work standalone, keep them separate and call two functions one after the other if need be.
   4. In the context of react, keep logic separated from react components. 
   5. Instead create custom hooks and use them. Hooks can be for making API calls, running some function (sorting, RMS checks etc...)
   6. css should be separated out from react always.
   7. css should be applied to the feature or main component, this css will automatically apply to every child component.

6. Build a strong base on react, understand all the hooks which react provides [React Hooks](https://react.dev/reference/react/hooks)
   1. More specifically dive deep into the less used hooks like useRef.
   2. These hooks have a very small set of use cases but they give a much better performance than other counterparts. 

7. React conventions [AirBnB Guide](https://github.com/airbnb/javascript/tree/master/react)
   1. Variable name should always be a noun
   2. Function name should always be a verb
   3. Component names and their files should always be **PascalCase**
   4. js functions and their files should be named in **camelCase**
   5. If the imports are going out of the second parent folder, use absolute imports instead of relative imports. (Prefer "Features/Orders/Services" over "../../Services")
   6. If there is more than 1 file in a folder, create an index.js in that folder so that the imports turn from **import MarketWatch "Features/Marketwatch/Marketwatch"** to **import {MarketWatch} "Features"**
   7. Always check the chrome console for warnings, aim to make it 0 warnings.

8. **ALWAYS** check for edge cases when writing code:
   1. Null edge cases. This is the bare minimum, your code should handle all sorts of null cases.
   2. Frontend should function even if the backend is down. Frontend should never throw an error to the user
   3. Understand the data structure which you're working on. js never throws errors for bad data structure unless you're accessing something from null or undefined; Not even for accessing the 6th element from an array of size 1.
   4. Burst requests should be blocked from frontend. Like disabling button once clicked.
   5. Thinking about these edge cases will also reduce load on the backend by removing unnecessary API calls.
   6. There will be many non-trivial edge cases which you'll only understand once you understand the feature completely.

9.  A very strong base in javascript is compulsory, understand the use case of each of these and use the best possible option. [W3 Schools](https://www.w3schools.com/jsref/)
    1.  Arrays, objects, dates
    2.  functions
    3.  loops like while, for and forEach
    4.  async, await
    5.  Read as much as possible in js, there's always something new in this, always some non-intuitive new behaviour.

10. React components should never be async
    1.  If API calls are needed, always put them inside useEffect or some controlled form.
    2.  If multiple API calls need to be in same function, make them in async with multiple promises and await Promise.All

> Ex. You're working at a bank, make an API to transfer funds.

> Ex. You're in zomato, make an API to place order.
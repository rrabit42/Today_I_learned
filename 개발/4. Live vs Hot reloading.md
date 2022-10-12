# Live Reloading vs Hot Reloading  
**Live reloading** reloads or refreshes the entire app when a file changes.   
For example, if you were four links deep into your navigation and saved a change,  
live reloading would restart the app and load the app back to the initial route.  
파일이 변경되면 전체 앱을 다시 로드하거나 새로고침.  

**Hot reloading** only refreshes the files that were changed without losing the state of the app.  
For example, if you were four links deep into your navigation and saved a change to some styling, the state would not change,  
but the new styles would appear on the page without having to navigate back to the page you are on because you would still be on the same page.  
파일이 변경되면 변경된 파일만 새로고침.  

그 외 hot restart, full restart 등  

## 출처  
https://stackoverflow.com/questions/41428954/what-is-the-difference-between-hot-reloading-and-live-reloading-in-react-native  

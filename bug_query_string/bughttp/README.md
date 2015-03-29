# bug http query parameter
### Run the application

### 1) go to the [Browser](http://localhost:8081/test?param1=1). With a parameter
http://localhost:8081/test?param1=1

### 2) now without the param [Browser](http://localhost:8081/test).
http://localhost:8081/test

### 3) now again with the param [Browser](http://localhost:8081/test?param1=1).
http://localhost:8081/test?param1=1

## Works Great!!!

# Now - The bug

## 1) Restart the application!

### 2) go to the [Browser](http://localhost:8081/test "API Console"). Without the parameter
http://localhost:8081/test

### 3) now again with the param [Browser](http://localhost:8081/test?param1=1).
http://localhost:8081/test?param1=1

### 4) oops!





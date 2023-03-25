```cppag-0-1gsa9ereoag-1-1gsa9ereo
cmake_minimum_required(VERSION 3.10)
project(task)


add_executable(task main.cpp)
find package(OpenCV REQUIRED)
target_include_directory(task PUBLIC ${OpenCV_INCLUDE_DIRS})
target_link_libraries(task ${Opencv_LIBS})
```

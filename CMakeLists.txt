# 设置 CMake 最低版本要求为 2.8.0
cmake_minimum_required(VERSION 2.8.0)

# 定义项目名称变量
set(PROJECT_NAME lichee_yolov5)

# 创建一个 C 和 C++ 项目
project(${PROJECT_NAME} C CXX)

# 设置 C++ 编译器为 riscv64-unknown-linux-musl-g++
set(CMAKE_CXX_COMPILER "riscv64-unknown-linux-musl-g++")

# 设置 C++ 编译器参数，包含 CPU 型号、指令集架构、代码模型和 ABI 等信息
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -mcpu=c906fdv -march=rv64imafdcv0p7xthead -mcmodel=medany -mabi=lp64d")

# 设置 C++ 标准为 C++11
set(CMAKE_CXX_STANDARD 11)

# 要求必须使用指定的 C++ 标准
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 启用 C++ 编译器的扩展特性
set(CMAKE_CXX_EXTENSIONS ON)

# 定义 TPU SDK 的路径，路径为当前 CMakeLists.txt 文件所在目录下的 TPU_SDK 文件夹
set(TPU_SDK_PATH ${CMAKE_CURRENT_LIST_DIR}/TPU_SDK)

# 检查 TPU_SDK_PATH 是否已经定义，如果未定义则输出错误信息并终止 CMake 配置
if(NOT DEFINED TPU_SDK_PATH)
  message(FATAL_ERROR "Please set TPU_SDK_PATH to point to the TPU_SDK installation")
endif()

# 将 TPU SDK 的头文件目录添加到编译器的搜索路径中
include_directories(${TPU_SDK_PATH}/include)

# 将 TPU SDK 的库文件目录添加到链接器的搜索路径中
link_directories(${TPU_SDK_PATH}/lib)

# 定义 OpenCV 的路径，路径为当前 CMakeLists.txt 文件所在目录下的 opencv 文件夹
set(OPENCV_PATH ${CMAKE_CURRENT_LIST_DIR}/opencv)

# 检查 OPENCV_PATH 是否已经定义，如果未定义则输出错误信息并终止 CMake 配置
if(NOT DEFINED OPENCV_PATH)
  message(FATAL_ERROR "Please set OPENCV_PATH to point to the opencvn installation")
endif()

# 将 OpenCV 的头文件目录添加到编译器的搜索路径中
include_directories(${OPENCV_PATH}/include)

# 将 OpenCV 的库文件目录添加到链接器的搜索路径中
link_directories(${OPENCV_PATH}/lib)

# 定义 CVI 相关的库文件列表，初始包含 cviruntime 和 cvikernel
set(CVI_LIBS ${CVI_LIBS} cviruntime cvikernel)

# 如果不是交叉编译环境，则将 cvicmodel 库添加到 CVI 相关的库文件列表中
if(NOT CMAKE_CROSSCOMPILING)
  set(CVI_LIBS ${CVI_LIBS} cvicmodel)
endif()

# 定义 OpenCV 相关的库文件列表，初始包含 opencv_core、opencv_imgcodecs 和 opencv_imgproc
set(OPENCV_LIBS ${OPENCV_LIBS} opencv_core opencv_imgcodecs opencv_imgproc)

# 如果不是交叉编译环境，则将 opencv_highgui 库添加到 OpenCV 相关的库文件列表中
if(NOT CMAKE_CROSSCOMPILING)
  set(OPENCV_LIBS ${OPENCV_LIBS} opencv_highgui)
endif()

# 定义额外的库文件列表，包含 dl、stdc++、pthread 和 z
set(EXTRA_LIBS ${EXTRA_LIBS} dl stdc++ pthread z)

# 添加一个可执行文件，源文件为 src 目录下的 detector_yolov5_fused_preprocess.cpp
add_executable(${PROJECT_NAME}
    ${CMAKE_CURRENT_LIST_DIR}/src/detector_yolov5_fused_preprocess.cpp)

# 将 CVI 相关库、OpenCV 相关库和额外的库链接到可执行文件上
target_link_libraries(${PROJECT_NAME}
    ${CVI_LIBS}
    ${OPENCV_LIBS}
    ${EXTRA_LIBS})
    
# 将生成的可执行文件安装到当前 CMakeLists.txt 文件所在目录下的 install 文件夹中
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_CURRENT_LIST_DIR}/install)
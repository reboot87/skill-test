# hello_world

一个最小的 CMake + CTest C++ 示例工程。

## 目录

```
CMakeLists.txt      # 项目定义（hello 可执行程序 + hello_test）
src/main.cpp        # 主程序
tests/test_hello.cpp# ctest 测试
```

## 本地构建

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=ON
cmake --build build
ctest --test-dir build --output-on-failure
./build/hello
```

Windows（多配置生成器）需要指定配置：

```powershell
cmake -B build -DBUILD_TESTING=ON
cmake --build build --config Release
ctest --test-dir build -C Release --output-on-failure
.\build\Release\hello.exe
```

## CI

| 文件 | 平台 | 触发 |
|---|---|---|
| `.github/workflows/ci.yml` | GitHub Actions | `push` 到 `main`、所有 PR |
| `Jenkinsfile` | Jenkins | Multibranch Pipeline 自动发现分支 / PR |

两边阶段一致：checkout → toolchain → configure → build → test → 归档产物（`build/hello`）。

GitHub 侧矩阵为 Debug / Release 两种构建类型；Jenkins 侧固定 Release。

### 密钥

本项目无发布、无部署，不需要任何 Secret / Credentials。

### Jenkins 前置配置

- Job 类型：Multibranch Pipeline，Script Path 指向仓库根的 `Jenkinsfile`。
- Agent 需支持 docker 容器（流水线使用 `ubuntu:24.04` 镜像并在容器内安装 cmake / ninja / ccache）。

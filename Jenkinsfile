// 由 cicd-generator 生成：C / C++ CI（Declarative Pipeline，CMake + CTest）
// 用 ubuntu 镜像并在容器内装工具链，避免依赖 agent 上预装的 cmake 版本
// 注意：docker agent 在容器内执行 checkout，基础镜像不含 git，所以 Toolchain 必须先于 Checkout
pipeline {
    agent {
        docker {
            image 'ubuntu:24.04'
            args '-u root -v ccache:/root/.ccache'
            reuseNode true
        }
    }

    options {
        timestamps()
        timeout(time: 40, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '20', artifactNumToKeepStr: '5'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }

    environment {
        CI             = 'true'
        CCACHE_DIR     = '/root/.ccache'
        CTEST_OUTPUT_ON_FAILURE = '1'
    }

    stages {
        stage('Toolchain') {
            steps {
                sh '''
                    set -euo pipefail
                    apt-get update
                    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends build-essential cmake ninja-build ccache git ca-certificates
                '''
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
                // 子模块：checkout scm 默认不递归
                sh 'git submodule update --init --recursive'
            }
        }

        stage('Configure') {
            steps {
                sh '''
                    set -euo pipefail
                    cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=ON -DCMAKE_C_COMPILER_LAUNCHER=ccache -DCMAKE_CXX_COMPILER_LAUNCHER=ccache
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    set -euo pipefail
                    cmake --build build -j"$(nproc)"
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'ctest --test-dir build --output-on-failure'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'build/Testing/**', allowEmptyArchive: true
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'build/hello', allowEmptyArchive: true
            cleanWs()
        }
    }
}

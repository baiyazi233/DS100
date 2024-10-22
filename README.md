Public-facing repo for materials used in the Fall 2024 iteration of Data 100 at UC Berkeley.

[环境配置](https://ds100.org/sp20/setup/)

–name 环境名称

–display-name 在jupyter notebook显示的别名

python -m ipykernel install --user --name tensorflow --display-name "tensorflow"

1 查看安装了哪些虚拟环境kernel（在base或虚拟环境下运行都可以）：
jupyter kernelspec list
2 删除指定的kernel：
jupyter kernelspec uninstall myenv
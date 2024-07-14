# Visual Studio Code Python 开发环境搭建

## 代码片段

```json {.line-numbers}
    "main": {
        "prefix": "ma",
		"body": [
            "if __name__ == '__main__':",
			"$1"
		],
		"description": "main 入口"
	},
	"import all": {
        "prefix": "imp",
		"body": [
            "import $1"
		],
		"description": "导入完整模块包"
	},
	"import module some thing": {
		"prefix": "fro",
		"body": [
            "from $1 import $2"
		],
		"description": "导入模块包的某个组件"
	},
	"init": {
        "prefix": "init",
		"body": [
            "def __init__(self):",
			"    $1"
		],
		"description": "类构造函数"
	},
	"del": {
        "prefix": "de",
		"body": [
            "def __del__(self):",
			"    $1"
		],
		"description": "类析构函数"
	},
	"async def func": {
        "prefix": "asdef",
		"body": [
            "async def $1($2):",
			"    $3"
		],
		"description": "定义异步函数"
	},
	"async def cls_func": {
        "prefix": "casdef",
		"body": [
            "async def $1(self$2):",
			"    $3"
		],
		"description": "定义类的异步函数"
	}
```

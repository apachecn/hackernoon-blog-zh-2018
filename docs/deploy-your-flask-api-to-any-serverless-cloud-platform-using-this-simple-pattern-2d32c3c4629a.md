# 使用这个简单的模式将您的 Flask API 部署到任何无服务器的云平台

> 原文：<https://medium.com/hackernoon/deploy-your-flask-api-to-any-serverless-cloud-platform-using-this-simple-pattern-2d32c3c4629a>

我将演示如何让 Flask 应用程序无服务器化，而不会将您局限于任何工具或云平台。它不需要任何应用程序代码的改变或者无服务器的[框架](https://hackernoon.com/tagged/frameworks)，所以你可以像以前一样继续本地开发。

理想情况下，您应该将您的应用程序重新架构成小的离散功能，以充分利用无服务器。然而，如果你想迁移一个遗留的应用程序或者只是一个快速开始使用无服务器的方法，那么这是一个很好的方法。

1.创建烧瓶应用程序
2。使应用程序无服务器
3。将 Lambda
4 的应用程序打包到 Zip 中。使用 Terraform 部署

# 1.创建烧瓶应用程序

我们将为 [Python](https://hackernoon.com/tagged/python) 3.6+创建一个 Flask 应用。

## 设置

首先创建一个虚拟环境并安装依赖项。

```
$ python3 -m venv env $ source env/bin/activate
$ pip install flask flask-lambda-python36 http-prompt
```

## 应用代码

添加下面的代码，它将创建一个具有完美音乐品味的 Flask 应用程序。

```
$ tree -L 4
.
├── app
│   ├── api
│   │   ├── __init__.py
│   │   └── resources.py
│   └── __init__.py
└── run.py# app/api/resources.py
from flask import Blueprint
from flask import request, jsonifyapi = Blueprint(‘api’, __name__)[@api](http://twitter.com/api).route(‘/artists’, methods=(‘GET’, ‘POST’))
def handle_artists():
    if request.method == 'POST':
        return 'ok'return jsonify([{'name': 'enya'}])# app/api/__init__.py
from .resources import api# app/__init__.py
def create_app(Flask):
    app = Flask(__name__)
    from .api import api as api_blueprint
    app.register_blueprint(api_blueprint)return app# run.py
from flask import Flask
from app import create_apphttp_server = create_app(Flask)
```

完整的应用程序代码可从[示例库](https://github.com/techjacker/python-serverless-api)获得。

## 运行应用程序

在本地开发模式下运行应用程序。

```
$ FLASK_DEBUG=1 FLASK_APP=run.py flask run
```

## 测试应用程序

在另一个终端窗口中手动测试 API。

```
$ http-prompt localhost:5000[http://localhost:5000](http://localhost:5000)> get artistsHTTP/1.0 200 OK
Content-Length: 31
Content-Type: application/json
Date: Thu, 12 Apr 2018 19:15:56 GMT
Server: Werkzeug/0.14.1 Python/3.6.5[
    {
        "name": "enya"
    }
]
```

# 2.使应用程序无服务器

到目前为止，这只是另一个烧瓶应用程序。是时候让它没有服务器了。将另一个名为`run-lambda.py`的文件添加到项目的根目录中。

```
# run-lambda.py
from flask_lambda import FlaskLambda
from app import create_apphttp_server = create_app(FlaskLambda)
```

就是这样！您的应用程序现在可以在 AWS Lambda 上运行了。这里的诀窍是使用 [FlaskLambda](https://github.com/sivel/flask-lambda) 代替普通烧瓶。FlaskLambda 将 AWS 提供给 Lambda 函数的有效负载转换成 Flask 所期望的格式。

这种适配器模式与 [zappa](https://github.com/Miserlou/Zappa) 使用的模式相同。就我个人而言，我不喜欢引入任何额外的工具依赖，而是选择 Terraform 或 [AWS 无服务器应用程序模型(SAM)](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) 进行部署。

请注意，FlaskLambda 只支持 Python <= 3.5\. I’ve created a [fork](https://github.com/techjacker/flask-lambda) ，该 fork 添加了对 Python 3.6+的支持，而 Python 3.6+是您之前用`pip install flask-lambda-python36`安装的。

# 3.将应用程序打包成 Lambda 的 Zip 文件

为了部署您的应用程序，您需要将您的应用程序打包成一个 zip 文件。以下脚本将捆绑您的应用程序代码和依赖项。

```
# bundlelambda.sh
#!/usr/bin/env bashOUTPUT_ZIP="$PWD/terraform/flask-app.zip"
ARCHIVE_TMP="/tmp/lambda-bundle-tmp.zip"addToZip() {
    local exclude_packages="setuptools pip easy_install"
    zip -r9 s"$ARCHIVE_TMP" \
        --exclude ./*.pyc \
        --exclude "$exclude_packages" \
        -- "${@}"
}
addDependencies() {
    local packages_dir=()
    packages_dir+=($(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())"))
    env_packages=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib(plat_specific=1))")
    if [[ "$env_packages" != "${packages_dir[0]}" ]]; then
        packages_dir+=($env_packages)
    fi
    for (( i=0; i<${#packages_dir[@]}; i++ )); do
        [[ -d "${packages_dir[$i]}" ]] && cd "${packages_dir[$i]}" && addToZip -- *
    done
}rm "$ARCHIVE_TMP" "$OUTPUT_ZIP"
addDependencies
addToZip app ./*.py
mv "$ARCHIVE_TMP" "$OUTPUT_ZIP"
```

这是示例存储库中脚本[的简化版本。](https://github.com/techjacker/python-serverless-api/blob/master/bin/bundlelambda)

保存并运行脚本，在`terraform/flask-app.zip`创建一个 zip 包。

```
$ chmod +x bundlelambda.sh
$ ./bundlelambda.sh
```

确保您在 Linux 平台上运行它，以确保与 Lambda 运行时环境的兼容性。

# 4.使用[地形](https://www.terraform.io/)部署

## 地形配置

我们将使用`[lambda-api-gateway](https://registry.terraform.io/modules/techjacker/lambda-api-gateway/aws/1.0.2)` terraform 模块来处理部署。在`terraform/main.tf`创建 Terraform 配置文件，并用您的 AWS 帐户 ID 更新`account_id`。

```
# terraform/main.tf
module "lambda_api_gateway" {
    source               = "[git@github.com](mailto:git@github.com):techjacker/terraform-aws-lambda-api-gateway"# tags
    project              = "todo-mvc"
    service              = "acme-corp"
    owner                = "Roadrunner"
    costcenter           = "acme-abc"# vpc
    vpc_cidr             = "10.0.0.0/16"
    public_subnets_cidr  = ["10.0.1.0/24", "10.0.2.0/24"]
    private_subnets_cidr = ["10.0.3.0/24", "10.0.4.0/24"]
    nat_cidr             = ["10.0.5.0/24", "10.0.6.0/24"]
    igw_cidr             = "10.0.8.0/24"
    azs                  = ["eu-west-1a", "eu-west-1b"]# lambda
    lambda_zip_path      = "flask-app.zip"
    lambda_handler       = "run_lambda.http_server"
    lambda_runtime       = "python3.6"
    lambda_function_name = "HttpWebserver"# API gateway
    region               = "eu-west-1"
    account_id           = "123456789"
}
```

这里的键值是`lambda_handler`，它指示 AWS Lambda 使用`run_lambda.py`文件作为我们应用程序的入口点。

## 部署

```
$ cd terraform
$ terraform apply
```

完成后，Terraform 应该输出 API URL，您可以使用它来手动测试部署是否成功。

```
$ http-prompt $(terraform output api_url)
GET artistsHTTP/1.0 200 OK
Content-Length: 31
Content-Type: application/json
Date: Thu, 12 Apr 2018 19:15:56 GMT
Server: Werkzeug/0.14.1 Python/3.6.5[
  {
    "name": "enya"
  }
]
```

# 结论

我们用一个现有的 Flask 应用程序，只用三行代码就把它变成了无服务器的！通过使用 adaptor 模式，我们给了自己在未来轻松更换无服务器云提供商的自由。我们甚至可以切换到基于容器的部署，而无需更改任何应用程序代码。

这种方法的好处:

*   像往常一样在本地开发(不需要模拟器)
*   全面了解和控制您的基础架构
*   避免云供应商锁定
*   避免应用程序代码框架锁定

未来的帖子将涵盖 [AWS 无服务器应用模型(SAM)](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) 、Azure 功能和谷歌云功能部署。

本教程的完整代码可从 [github](https://github.com/techjacker/python-serverless-api) 获得。

*原载于*[*andrewgriffithsonline.com*](https://andrewgriffithsonline.com/blog/180412-deploy-flask-api-any-serverless-cloud-platform)*。Andrew Griffiths 是一名自由开发人员，专门使用无服务器平台构建和部署 Golang、Python 和 JavaScript 应用程序。*
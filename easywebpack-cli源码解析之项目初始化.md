# easywebpack-cli源码解析之项目初始化

这几天一直在看 @sky 的<a href="http://easyjs.cn/easywebpack/">easywebpack</a>这一套前端工程化解决方案,在使用过程中非常的想了解这一套解决方案的思想,所以我开始了研究他的源码,先来看看 @sky 对这一套方案的介绍:

---
  easywebpack 是什么easywebpack 是基于 webpack 的前端工程化解决方案。旨在解决 webpack 项目构建复杂,使用成本高,复用低,维护成本高等工程效率问题。基于 easywebpack 工程化方案, 你能非常简单容易的对各种前端项目进行工程化建设，及时享受最新的特...

---

根据这个介绍,可以理解为这套方案旨在通过工程的方法来解决webpack 项目构建复杂,使用成本高,复用低,维护成本高等工程效率问题,我打算从项目的开始来研究,先从cli工具 <a href="https://github.com/easy-team/easywebpack-cli">easywebpack-cli</a>开始,我计划阅读源码同时添加注释来了解easywebpack-cli的实现原理,这里我主要介绍easywebpack-cli在初始化一个项目的时候他的实现过程

## 1,fork easywebpack-cli

 首先我fork了<a href="https://github.com/easy-team/easywebpack-cli">easywebpack-cli</a>,以便我记录自己的注释,项目地址 https://github.com/ZGL520/easywebpack-cli, 还有一部分没有注释完成,后续继续更上

## 2,项目目录

![img](https://github.com/ZGL520/MyImages/blob/master/21.jpeg?raw=true)

## 3,项目是怎样被初始化的

### 1, easy init 的开始

当我们在命令行中输入如下命令并按下回车键的时候,项目的初始化就开始了

    easy init app

这里首先调用的是Command类的init方法

```javascript
// lib/command.js
  init() { // 初始化项目
    this.program
      .command('init')
      .option('-r, --registry [url]', 'npm registry, default https://registry.npmjs.org, you can taobao registry: https://registry.npm.taobao.org')
      .option('--sync [url]', 'sync easy init prompt template config')
      .description('init webpack config or boilerplate for Vue/React/Weex')
      .action(options => {
        this.action.init(this.boilerplate, options);
      });
  }
```

在上面Command类中的init方法中最后调用了Action类中的init方法

```javascript
// lib/action.js
  init(boilerplate, options) {
    if (options.sync) { //同步最新的cli配置
      const filepath = path.resolve(__dirname, 'ask-sync.js');
      const url = options.sync === true ? process.env.EASY_INIT_ASK_URL || 'https://raw.githubusercontent.com/easy-team/easywebpack-cli/master/lib/ask.js' : options.sync;
      utils.request(url).then(res => {
        fs.writeFileSync(filepath, res.data);
        console.log(`${chalk.green('easy sync successfully, please run [easy init] again')}`);
      }).catch(err => {
        console.log(chalk.red('easy sync error:'), err);
      });
    } else {
      return new Boilerplate(boilerplate).init(options);
    }
  }
```

### 2, 模板选择 Boilerplate类

```javascript
  new Boilerplate(boilerplate).init(options)
```

到这里另一个类出场了,Boilerplate, 找到 lib/init.js 下,同样发现了init方法,很明显模板的选择是在这里完成的,这里模板的选择逻辑上分为两步,第一步是选择技术栈方向,比如react技术栈,或者vue技术栈,第二步是同一技术栈方向会有多个更加细化的模板选择,具体流程看下面源码和注释

```javascript
  // lib/init.js
  init(options) {
    inquirer.prompt([{ // 选择模板
      type: 'list',
      name: 'boilerplateName',
      message: 'please choose the boilerplate mode?',
      choices: this.boilerplateChoice
    }]).then(boilerplateAnswer => {
      const boilerplateName = boilerplateAnswer.boilerplateName;
      const boilerplateInfo = this.getBoilerplateInfo(boilerplateName);//获取选择模板信息
      const choices = boilerplateInfo.choices;
      const download = new Download(options);
      if (this.boilerplateDetailChoice[boilerplateName]) {
        const boilerplateDetailAsk = [{ //如果存在分支,选择模板分支
          type: 'list',
          name: 'project',
          message: 'please choose the boilerplate project mode?',
          choices: this.boilerplateDetailChoice[boilerplateName]
        }];
        inquirer.prompt(boilerplateDetailAsk).then(boilerplateDetailAnswer => { //获得模板分支选择信息
          const project = boilerplateDetailAnswer.project;
          const bilerplateInfo = this.getBoilerplateDetailInfo(boilerplateName, project);
          const projectInfoChoice = this.getProjectAskChoices(bilerplateInfo.choices || choices);
          inquirer.prompt(projectInfoChoice).then(projectInfoAnswer => {//项目信息配置
            download.init(this.projectDir, bilerplateInfo, projectInfoAnswer);//下载并创建项目
          });
        });
      } else { //如果没有分支
        const pkgName = boilerplateInfo.pkgName || boilerplateName;
        const projectInfoChoice = this.getProjectAskChoices(choices);
        inquirer.prompt(projectInfoChoice).then(projectInfoAnswer => {//项目信息配置
          const specialBoilerplateInfo = { pkgName, run: boilerplateInfo.run };
          download.init(this.projectDir, specialBoilerplateInfo, projectInfoAnswer);//下载并创建项目
        });
      }
    });
  };
```

在上面的Boilerplate的init方法中主要选择进行模板选择,同时还配置了项目信息和下载并创建项目,这两项在接下里会讲到,模板的配置选项位于 lib/ask.js

```javasctipt
// lib/ask.js
/**
 * 模板配置选项
 */
exports.boilerplateChoice = [
  ...
];

/**
 * 不同模板分支配置选项
 */
exports.boilerplateDetailChoice = {
  ...
};

/**
 * 项目配置选项
 */
exports.projectAskChoice = [
  ...
];
```

### 3, 配置项目信息

配置项目信息的配置项同样在 lib/ask.js下

```javascript
          inquirer.prompt(projectInfoChoice).then(projectInfoAnswer => {//项目信息配置
            download.init(this.projectDir, bilerplateInfo, projectInfoAnswer);//下载并创建项目
          });
```
```javascript
        inquirer.prompt(projectInfoChoice).then(projectInfoAnswer => {//项目信息配置
          const specialBoilerplateInfo = { pkgName, run: boilerplateInfo.run };
          download.init(this.projectDir, specialBoilerplateInfo, projectInfoAnswer);//下载并创建项目
        });
```

如上源码中主要是项目信息的配置,例如项目名字,作者,项目描述等,项目的下载并创建请看下面Download类

### 4, 下载并创建项目

在这里Download类登场,这个类会去执行具体的模板加载以及项目文件的创建等工作,同样来看Download类的init方法

```javascript
  // lib/download.js
  init(root, bilerplateInfo, projectInfoAnswer = {}, options = {}) {
    const self = this;
    const { pkgName, sourceDir = '', run, value } = bilerplateInfo;
    const { name, npm } = projectInfoAnswer;
    const projectName = name || value || pkgName;

    //初始化项目
    co(function *() {
      const absSourceDir = yield self.download(pkgName, sourceDir);//下载模板文件
      const absTargetDir = path.join(root, projectName);//连接项目文件根目录
      yield mkdirp(absTargetDir);//创建项目文件夹
      self.copy(absSourceDir, absTargetDir);//copy模板文件到项目文件夹
      self.copyTemplate(absTargetDir); // copy模板
      self.updatePackageFile(absTargetDir, projectInfoAnswer); // 更新项目信息
      utils.log(`init ${projectName} project successfully!\r\n`);
      self.installDeps(absTargetDir, { npm }); // 安装项目依赖库
      self.quickStart(projectName, { npm, run }); //快速开始提示
    }).catch(err => {
      /* istanbul ignore next */
      console.log('init error', err);
    });
  }
```

这里的init方法进行了一系列的实际项目创建操作,这里还调用的一些Download类的其他方法来完成相关操作,这些方法基本都做了注释,可到项目中查看

## 4,总结

在项目的初始化中,主流程用到了下面几个类

    Command
    Action
    Boilerplate
    Download


## 5,插曲

我在全局安装easywebpack-cli的时候遇到了下面的警告和错误,这是因为我mac没有安装xcode的原因,但是这并不影响cli的正常使用,装上xcode后不会出现此问题

![img](https://github.com/ZGL520/MyImages/blob/master/20.jpeg?raw=true)
    

最后,如果有错误,欢迎指教
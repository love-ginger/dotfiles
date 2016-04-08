# 打造极致的开发环境

这份配置文件仓库记载了一台全新的 Mac OS X 机器的开发环境从无到有，从有到精的全过程。

> 注意：本仓库的根路径实际上对应的实际路径应该是 `~/.config`，如果你要直接拿去用的话，最好使用 `ln` 路径去做对应的软链接。例如 `~ $ ln -s .config/.bashrc .bashrc`，这样的话会保证文件在正确的路径下并且编辑它们会直接改变位于 `~/.config` 路径下对应文件的内容。这其中有一些文件是不需要做软链接的，因为它们本来就应该位于 `~/.config` 下（比如 `~/.config/nvim/**`）。后面我会写一个 `~/.config/install.sh` 脚本来自动化做软链接的过程，但不是现在因为要补充的文件还有很多很多……

至本仓库创建之前，已经完成的配置列举如下：

1. 安装 Command Line Tools

	配置开发环境的第一步是安装 Mac OS X 下最好的系统软件包管理器 [Homebrew](http://brew.sh/index_zh-cn.html)，但是首先 Homebrew 需要依赖 [Command Line Tools](https://developer.apple.com/downloads/)。这个软件包为 Terminal 命令行环境安装了进行 Unix 风格开发所必需的工具，同时包含 OS X SDK 及相关的头文件，另外还包括了诸如 Apple LLVM 编译器等重要的工具。如果安装了 Xcode，那么 Command Line Tools 已经集成在了 Xcode 的软件包里（可能需要在偏好设置里确认安装），然而如果不需要做 OS／iOS 开发的话，Xcode 的体积过大了，所以可以从苹果开发者网站上下载单独的 Command Line Tools 安装包，大小只有 160MB 左右。

2. 安装 Homebrew

	Homebrew 是接下来安装一系列软件包的前置依赖工具，个人习惯认为优秀的开发环境应该尽可能避免手工下载 -> 安装这样的工作，取而代之，用命令行完成所有可能的软件安装和环境配置工作。要想达成这一点 Homebrew 是必不可缺的！Homebrew 的安装过程很简单，只需要在终端执行如下命令：

	```bash
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	```

	这条命令需要兼容 bash 的 shell 环境，Mac OS X 自带的终端默认的环境就是 bash，无须更改；另外 Mac OS X 内置了 Ruby，版本也足够用（之后我们会利用 Homebrew 来安装和管理多版本的 Ruby，此时用系统内置的版本即可）。然而坑爹的是，由于国内的网络环境影响（你懂的），安装 Homebrew 的过程极有可能不太顺利，所以你需要预先准备好科学上网的措施。对于我个人来说，惯用的科学上网手段也需要先有 Homebrew 来辅助，所以这里让我无比蛋疼……终于还是找了一个 VPN 账号，先用系统自带的 VPN 连接搞定了 Homebrew 的安装。

	> 默认的终端用来安装前面的步骤没有问题，不过它很丑－－在这里我做了额外两件事情：第一，安装了适合 `Terminal.app` 使用的 [Material Theme](https://gist.github.com/mvaneijgen/4c56701215847dd5ddcf)；第二，安装了 [Input Font](http://input.fontbureau.com/)。这些都是为了让终端用起来稍微舒服一些，但最终我要用 iTerm2 取代默认的终端，所以它们都是可选的步骤。

3. 配置与扩展 Homebrew

	Homebrew 的使用非常简单，但若要发挥它的潜力让它更好的工作还需要进一步打磨。首先安装 [Homebrew Cask](http://caskroom.io/) 来扩展 Homebrew 的使用场景。Homebrew 本身主要是安装管理命令行下的系统软件包的，图形界面下的软件它就管不着了。但是 Homebrew Cask 则可以让我们进一步使用 Homebrew 来安装 GUI 软件，如此一来几乎所有的软件包安装管理的方式就可以统一了（App Store 负责剩下的一部分软件，另外还有极个别是需要手工搞定的）。

	> Homebrew Cask 默认把软件统一安装在 `/opt/homebrew-cask/Caskroom` 下并把对应的软链接创建在 `~/Applications` 下，这两个缺省值都不合我的胃口，我习惯于把软件安装在 `/usr/local/Caskroom` 下（和 Homebrew 的 `Cellar` 同级），软链接创建在 `/Applications` 下（我的机器只有我自己用），因此在 `.bash_profile` 下添加了这样一句：`export HOMEBREW_CASK_OPTS="--appdir=/Applications --caskroom=/usr/local/Caskroom"`；此为可选项。

	使用 Homebrew Cask 安装的第一个 GUI 软件是神器 Alfred，实际上最新的 Spotlight 已经足够好用了，只不过 Alfred 提供了双击热键激活的功能，这是我个人离不开的（我用双击 <kbd>option</kbd> 来打开 Alfred，Spotlight 无法定义这样的激活热键，很遗憾）。

	> 如果改变了 `--caskroom` 选项的路径，不要忘记同步修改 Alfred 查找应用的路径，否则找不到安装的软件。

	接着可以顺手把 Google Chrome 也装上，Safari 就可以去休息了🛌 不过 Homebrew Cask 缺省只有 Chrome 的最新稳定版，而我习惯用开发版（dev channel），所以还得先扩展一下 Homebrew Cask。执行 `brew tap caskroom/versions` 之后就可以搜索和安装到软件的不同版本了（不只是 Chrome），顺便可以执行 `brew tap homebrew/versions` 让 Homebrew 自己也可以安装和管理一些系统软件包的多个版本（比如各种 PHP）。

4. 完善科学上网机制

	作为开发者不会科学上网是万万不行的。这不，安装 Google Chrome 的时候就碰壁了……此时已经可以安装和配置基于 Shadowsocks 的科学上网环境了。

	先使用 Homebrew Cask 安装 ShadowSocksx 客户端（不要问我代理服务器上哪儿找，请自力更生）解决操作系统层面下的科学上网问题，不过命令行环境下会稍有一些棘手，因为 Shadowsocks 的代理无法直接设置给终端使用。没关系，祭出 [Polipo](http://www.pps.univ-paris-diderot.fr/~jch/software/polipo/) 的时候到了。

	Homebrew 可以直接安装 Polipo，但是需要做一些工作让它和 Shadowsocks 协同干活。首先 Polipo 作为终端下的服务是需要启动／停止的，这里有两个选择：一）手动；二）开机自动。手动的话推荐使用 [Homebrew Services](https://github.com/caskroom/homebrew-versions) 来简化操作，执行 `brew tap homebrew/services` 之后就可以使用 `brew services start|stop|restart SERVICE_NAME` 这样的命令来操作一切终端服务了（以后会安装类似 Polipo 的软件，比如 MongoDB MySQL PostgreSQL 等等都可以用这个来操作）；此外还有一个 GUI 版本的服务管理器叫 [LaunchRocket](https://github.com/jimbojsb/launchrocket) 可以推荐一下，不过我是习惯一切都在命令行下搞定的。

	至于开机自启动嘛，`brew info polipo` 就会告诉你该怎么做，唯一的问题是我们需要 Polipo 在启动的时候要设定 `socksParentProxy` 选项。网上很多教程都是让去修改 `/usr/local/opt/polipo/homebrew.mxcl.polipo.plist` 文件（参考：http://qichunren.github.io/tool/2014/07/15/Convert-shadowsocks-into-http-proxy-on-mac/ ），但这个方法太二了，万一以后升级了 Polipo 还得再改吗？其实人家 Polipo 支持配置文件的嘛！在 `~/.polipo` 文件里加一句 `socksParentProxy = "localhost:1080"` 就好了。

	最后在 `~/.bash_profile` 里加两句：

	```bash
	export http_proxy=localhost:8123
	export ALL_PROXY=$http_proxy
	```

	终端下的 HTTP 代理就此搞定，现在可以愉快的安装 Google Chrome Dev 了。

	另外，[`ShadowSocksx/user-rule.txt`](/ShadowSocksx/user-rule.txt) 是我自己使用的自定义过滤列表（出现在表内的项会在自动代理模式下也通过 ss 代理），它不一定符合你的网络状况，所以请有选择使用。如果你要直接用的话需要执行：`~ $ ln -s .config/ShadowSocksx/user-rule.txt .ShadowSocksx/user-rule.txt`。同时要注意，每次修改了这个文件都要执行 `从 GFWList 更新 Pac` 的菜单选项以便重新载入用户配置文件（在 ShadowSocksx 客户端的菜单内）。

5. 使用 Homebrew 安装／更新 Bash(w/ Homebrew-ish completions), Python2/3, rbenv(Ruby), git, hub...

	这些都是很常规的东西，不需要逐个解释了，在安装它们的过程中，绝大多数的系统依赖都会被 Homebrew 搞定。

	安装完 git 之后别忘了设置全局的基本参数（见相关 commits）；安装完 hub 之后，不要忘了还原／更新 Github 的 token。

	正确设置 ssh 相关的东西之后便可以初始化本仓库了，在整理了已有的配置文件之后便可以准备提交了，不过在第一次提交之前要准备好 `README.md` 文档把之前的过程记录下来（也就是现在看到的这些）。那么为了编写文档，还得安装 Vim，啊不，以后改换 [Neovim](https://neovim.io) 了。`brew install neovim/neovim/neovim` 是正确的安装命令（蛋疼的名字？组织名／仓库名／配方名而已）。

6. 安装 iTerm2

	最近 iTerm2 的开发很活跃，推荐使用 Homebrew Cask 安装 `iterm2-beta`。和 Terminal.app 用过的 Material Theme 对应的主题在这里：[Material Theme for iTerm2](https://github.com/MartinSeeler/iterm2-material-colors/blob/master/material-design-colors.itermcolors)。

7. 关于 Neovim

	添加了 [关于 Neovim](/neovim.md) 文件，持续更新关于 Neovim 的各种知识和技巧。

8. 关于 NVM

	**使用 NVM 的理由（而不使用 n 或其他版本管理器）**

	我知道相比 NVM 而言，n 更加的轻量而且不会改变 Shell 变量，但是 NVM 提供了一个在安装新版本后直接导入旧版本全局安装的 npm 模块的功能，对我这种没事穷折腾的人来说还是挺无脑的。虽然我也知道写一点简单的脚本可以让 n 也这样无脑，可谁让我习惯了撒……

	但坑爹的是不久前 NVM 不再支持从 Homebrew 直接安装了，这就迫使我需要单独记忆多一种东西的安装和升级🙀

	这里我就不打算事无巨细捋一遍了（谁知道将来还会不会变？），还是直接看 Github Repo 更靠谱（可以直接 `$ brew home nvm` 快捷打开）。不过我需要讲一下升级的要点：

	- 第一步：进入 `$NVM_DIR` 路径，`$ cd "$NVM_DIR"`

	- 第二步：更新代码，`$ git pull origin master`；注意因为是 Detached HEAD，所以 branch tracking 是不行的，老老实实写全咯

	- 第三步：检出最新的 Tag，`$ git checkout $(git describe --abbrev=0 --tags)`；这条命令还是蛮有用的，建议整个快捷方式

9. 关于字体

	我发现我漏了谈谈关于字体的事情。长久以来我都是 [Input](http://input.fontbureau.com/) 的忠实粉丝，主要是应为它可以方便的定制字体的细节因此极大满足了我的龟毛心理。不过问题也就随之而来了：每一次都想重新调整一番，次数多了就开始觉得不值得浪费时间在这上面，所以这一次我决定放弃 Input 了！

	新的选择是 [Hack](http://sourcefoundry.org/hack/)，一度是我的第一备选，知道前不久有了 Homebrew 配方我突然意识到：其实字体也可以不用下载的嘛～安装细节说一下：

	```shell
	$ brew tap caskroom/fonts  # 于是就有很多字体可装
	$ brew cask install font-hack
	```

	That's it!

10. Material Design 系统色板

	挺有用的小玩意儿，可以安装在 Mac OS X 的系统取色器里，于是在任何应用里都可以使用统一的配色方案了。

	![material-design-system-colorpicker](https://raindrop.io/files/drop15536861/15536861_2016-01-31-4.png)

	> 注：文件在 `/misc` 路径下

11. 关于 Git/Github 与 GPG

	不久前，[Github 正式支持了 GPG 签名校验](https://github.com/blog/2144-gpg-signature-verification)，这是一件好事。我曾经参与过一个要求 GPG Signing 的开源项目有过一点经验，所以准备过程还算顺利。

	一位同行在第一时间发布了[一份 step by step 的教程](https://github.com/pstadler/keybase-gpg-github)，不过这份教程牵涉到了一个 [keybase.io](https://keybase.io/) 的第三方服务，keybase 有点坑爹，我大半年前就开始申请 private beta 测试了，到现在还没轮到……

	以下是不牵涉 keybase.io 的流程，和上面的教程其实差不太多，只是个别命令有差异：

	- 第一步：确认 `git --version` >= 2.0.0，否则用 `brew install git` 升级；

	- 第二步：`brew install gpg`，然后 `gpg --list-keys` 看看有没有现存的秘钥。通常没用过 GPG 的话应该是没有的，若有的话直接跳到第四步；

	- 第三步：`gpg --gen-key` 生成一份秘钥，类型用默认的，秘钥大小选 `4096`，有效期自便。后面写 ID 的部分要注意：姓名、昵称、电子邮件三项是分三步填写的，不注意看的话会以为一次写全，于是你就永远也完成不了；另外如果该秘钥用于 Github，那么电子邮件一定要用 Github 上验证过的，否则无法在 Github 上有效使用。passphrase 的部分，和 SSH 一回事儿，自己看着办；

	- 第四步：`gpg --list-keys` 把刚才创建好的秘钥显示出来，你会看到类似下面的结果：

		```shell
		$ gpg --list-keys
		/Users/hubot/.gnupg/pubring.gpg
		------------------------------------
		sec   4096R/A8F99211 2016-04-05
		uid                  Hubot 
		ssb   4096R/Z832QR89 2016-04-05
		```

		`sec` 那一行是接下来我们需要的，`A8F99211` 就是你的 _public GPG key_。

	- 第五步：`git config --global user.signingkey A8F99211`，告诉 Git 关于你的 GPG 秘钥的信息。还有一个可选的步骤：`git config --global commit.gpgsign true`，这个会在提交的时候默认使用 GPG 签名，就不用 `-S` 了；

	- 第六步：`open https://github.com/settings/keys`，打开自己的 Github 设置页面，就像配置 SSH 那样添加一个新的 GPG 秘钥设置，里面的内容这样获取：`gpg --armor --export A8F99211 | pbcopy` 然后粘贴进去即可；

	- 第七步（可选）：每次带 GPG 签名都会问你要密码会很烦，解决的办法见之前提到的教程。

---

_To Be Continued..._

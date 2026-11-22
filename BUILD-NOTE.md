# dsh-zh-ui 本地构建备注

这个目录不是从 npm 装的，是本地编译出来的，原因见下。

## 来源

- 仓库：https://github.com/1186258278/dsh-zh-ui
- commit：4ddff4f2e320285f003b0c7ca74a152440c5d255（v0.2.0）
- 许可证：MIT

## 为什么本地编译

这个包在 npm 上没有发布版，只能从 git 装；而它的 `prepare` 是 `npm run build`（tsc 编译出 `lib/`）。
当前这台机器的 DSH 是打包版，PATH 上没有 `node` / `npm` / `pnpm`，pnpm 准备 git 包时调用 `npm install` 会直接失败
（`'npm' 不是内部或外部命令`），在应用内安装走不通。

## 构建方式

用 DSH 自带运行时：

- node：`C:\Users\Administrator\AppData\Local\Programs\DSH NEXT\resources\runtime\x64\primary-runtime\dependencies\node\bin\node.exe`
- pnpm：`...\dependencies\pnpm\bin\pnpm.cjs`

步骤（已执行）：

1. 从 codeload 下载该 commit 的 tar.gz 并解压
2. `node pnpm.cjs install --ignore-scripts`（装 devDeps，含 typescript 5.9.3）
3. `node node_modules/typescript/bin/tsc -p tsconfig.json --noEmitOnError false`
4. 把整个目录挪到 `C:\Users\Administrator\.dsh\local-plugins\dsh-zh-ui`
5. 在 DSH 里按目录路径安装（pnpm link）

产物：`lib/index.js`、`lib/lint.js`、`lib/index.d.ts`、`lib/lint.d.ts`

## 升级/重装怎么做

1. 取新 commit 的源码，重复上面 2–3 步
2. 把新的 `lib/` 覆盖到本目录
3. DSH → 插件 → 卸掉 dsh-zh-ui → 再按目录路径装一次

注意：这样装的包**不能**在插件页点「更新」；上游发版后需要手动重来一遍。
`node_modules/` 只在构建时需要，运行时不依赖它（peer 依赖由宿主提供）。

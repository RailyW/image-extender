# Image Extender Skills 模块

本目录保存从原 Next.js 工作台抽离出来的 Codex Skill。Skill 的入口文件位于 `image-extender-studio/SKILL.md`，固定的图像处理、provider 调用、切图、打包和 manifest 生成逻辑位于 `image-extender-studio/scripts/image_extender_skill.py`。

该模块的目标不是替代原 Web 应用，而是让 Codex 可以在没有前后端工作台的情况下，用 Markdown 编排、提示词工程和可重复执行的 Python 脚本完成同一组工作流。

Sprite 子流程的确定性后处理也位于同一脚本中：`sprite process` 会切出 4×2 单帧、清理低 alpha 残边、固定 grounded 动画脚底 baseline，并用上身分位数锚点稳定水平位置。输出的 `manifest.json` 会记录每帧对齐前后的 bbox、baseline、anchor 和平移量，方便判断抖动来自切图坐标还是源图内部绘制不一致。

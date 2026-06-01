# Image Extender Skills 模块

本目录保存从原 Next.js 工作台抽离出来的 Codex Skill。Skill 的入口文件位于 `image-extender-studio/SKILL.md`，固定的图像处理、provider 调用、切图、打包和 manifest 生成逻辑位于 `image-extender-studio/scripts/image_extender_skill.py`。

该模块的目标不是替代原 Web 应用，而是让 Codex 可以在没有前后端工作台的情况下，用 Markdown 编排、提示词工程和可重复执行的 Python 脚本完成同一组工作流。

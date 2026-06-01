# 子流程：Sprite 动画

## 输入

- 角色 / 生物 prompt。
- 体型方案：`biped`、`quadruped`、`serpent`、`flyer`、`blob`。
- 动画：按体型选择，例如 `walk`、`run`、`slither`、`flap`、`hop`。

## 标准流程

1. 调用 `prompt generate --mode sprite-anchor` 生成 anchor prompt。
2. 用 provider 或 Codex App `imagegen` 生成单角色 anchor。
3. 调用 `sprite guide` 生成 4×2 pose guide。
4. 调用 `prompt generate --mode sprite-sheet` 生成 sheet prompt，并引用 anchor / pose guide。
5. 用 provider 或 Codex App `imagegen` 生成 4×2 sprite sheet。
6. 调用 `sprite process` 执行色键、切片、主连通域隔离、scale 归一、baseline 对齐、水平居中。
7. 调用 `sprite package` 导出 grid、strip、单帧 PNG 和 manifest。
8. 如果启用 vision QA，调用 `prompt review --kind sprite` 与 `review call`。

## 关键命令

```bash
python3 skills/image-extender-studio/scripts/image_extender_skill.py sprite guide --body-plan quadruped --anim run --output pose-guide.png
python3 skills/image-extender-studio/scripts/image_extender_skill.py prompt generate --mode sprite-sheet --body-plan quadruped --anim run --prompt "armored wolf" --output sprite-prompt.txt
python3 skills/image-extender-studio/scripts/image_extender_skill.py sprite process --sheet generated.png --body-plan quadruped --anim run --output-dir sprite
python3 skills/image-extender-studio/scripts/image_extender_skill.py sprite package --input-dir sprite --output sprite.zip
```

## 验收

- 8 帧 PNG 齐全。
- grid sheet、horizontal strip、manifest 齐全。
- manifest 包含 FPS、loop、frame size 和坐标。

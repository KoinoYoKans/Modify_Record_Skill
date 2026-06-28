# Modify Record Skill

自动记录代码修改历史的 Claude Code 技能。对话中发生代码修改时，自动将 diff、用户需求、时间戳按天保存到项目内的 `{项目名}_Modify/` 目录。

## 功能

- 🕐 按天记录，自动创建 `{项目名}_Modify/` 目录
- 💬 保留用户原始需求描述
- 📄 diff 格式代码对比，一眼看出改了什么
- 🗑️ 文件删除 / 📎 文件重命名也会记录
- 📊 自动生成月度索引（表格 + 统计）
- 🚫 自动过滤 `node_modules/`、`dist/`、锁文件等非项目文件
- ⏸️ 支持"暂停记录"/"恢复记录"/"这次跳过"等控制指令

## 示例

### bug 修复

```markdown
### 🕐 2026-06-28 14:32:15 | `bug修复`

> 💬 "登录页面邮箱验证有问题，输个 a@b 就能通过，帮我修一下"

📝 **需求**：修复登录页面邮箱格式校验过于宽松的 bug

📄 `src/components/LoginForm.tsx`

  const validateEmail = (email) => {
-   return email.includes('@');
+   const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
+   return re.test(email);
  };
```

### 多文件重构

```markdown
### 🕐 2026-06-28 17:45:20 | `重构`

> 💬 "把 UserService 改名叫 AccountService，更新所有引用"

📝 **需求**：重命名 UserService 并更新所有引用文件

📎 `src/services/UserService.ts` → `src/services/AccountService.ts`

📄 `src/services/AccountService.ts`

- export class UserService {
+ export class AccountService {
    private baseUrl = '/api/users';

📄 `src/components/UserProfile.tsx`

- import { UserService } from '../services/UserService';
+ import { AccountService } from '../services/AccountService';
- const userService = new UserService();
+ const accountService = new AccountService();
```

### 文件删除 + 迁移

```markdown
### 🕐 2026-06-28 16:10:33 | `文件删除`

> 💬 "那个旧的 helper 文件不用了，删掉吧"

📝 **需求**：删除已废弃的 helper 文件

🗑️ `src/utils/oldHelper.ts`

**删除原因**：已被 `src/utils/newHelper.ts` 替代
```

## 输出结构

```
{项目根目录}/
├── {项目名}_Modify/
│   ├── {项目名}_Modify_2026-06.md          ← 月度索引（表格+统计）
│   ├── {项目名}_Modify_2026-06-28.md       ← 日记录（diff 详情）
│   └── ...
└── .gitignore   ← 自动忽略 *_Modify/
```

## 用户控制

| 指令 | 效果 |
|------|------|
| "暂停记录" | 当前对话停止记录 |
| "恢复记录" | 恢复自动记录 |
| "这次跳过" | 仅跳过当前这一次 |
| "强制记录" | 即使文件在忽略列表也记录 |

## 记录类型

| 类型 | 说明 |
|------|------|
| `功能新增` | 添加新功能、新模块、新接口 |
| `bug修复` | 修复已知的 bug 或错误 |
| `重构` | 不改变功能的代码结构调整、优化 |
| `配置变更` | 修改配置文件、环境变量、构建脚本等 |
| `文件删除` | 删除文件或目录 |
| `文件重命名` | 重命名或移动文件 |
| `文档修改` | 修改 README、注释、文档等 |
| `依赖变更` | 添加、升级、移除依赖包 |

## 安装

```bash
git clone https://github.com/KoinoYoKans/Modify_Record_Skill.git
claude skill add ./Modify_Record_Skill/modify-record-skill
```

## 许可证

MIT License

## ADDED Requirements

### Requirement: 预设 Mock 用户数据
系统 SHALL 通过 Flyway 迁移脚本预设若干 Mock 用户，每个用户包含昵称和头像 URL。

#### Scenario: 系统启动后 Mock 用户数据可用
- **WHEN** 系统启动并完成数据库迁移
- **THEN** 数据库中存在 5-8 个预设的 Mock 用户，每个用户包含 id、nickname 和 avatar_url

### Requirement: 获取 Mock 用户列表
系统 SHALL 提供获取 Mock 用户列表的接口，供前端发帖时选择身份使用。

#### Scenario: 获取所有 Mock 用户
- **WHEN** 前端请求 Mock 用户列表
- **THEN** 系统返回所有预设 Mock 用户的 id、nickname 和 avatar_url

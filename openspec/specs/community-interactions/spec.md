## ADDED Requirements

### Requirement: 点赞帖子
系统 SHALL 允许用户对帖子进行点赞或取消点赞，同一用户对同一帖子只能点赞一次。

#### Scenario: 点赞一个帖子
- **WHEN** 用户对一个未点赞的帖子执行点赞操作
- **THEN** 系统记录点赞，帖子的 like_count 加 1，返回当前点赞状态为已点赞

#### Scenario: 取消点赞一个帖子
- **WHEN** 用户对一个已点赞的帖子再次执行点赞操作
- **THEN** 系统取消点赞，帖子的 like_count 减 1，返回当前点赞状态为未点赞

### Requirement: 收藏帖子
系统 SHALL 允许用户收藏或取消收藏帖子，同一用户对同一帖子只能收藏一次。

#### Scenario: 收藏一个帖子
- **WHEN** 用户对一个未收藏的帖子执行收藏操作
- **THEN** 系统记录收藏，帖子的 favorite_count 加 1，返回当前收藏状态为已收藏

#### Scenario: 取消收藏一个帖子
- **WHEN** 用户对一个已收藏的帖子再次执行收藏操作
- **THEN** 系统取消收藏，帖子的 favorite_count 减 1，返回当前收藏状态为未收藏

### Requirement: 点赞评论
系统 SHALL 允许用户对评论进行点赞或取消点赞，同一用户对同一评论只能点赞一次。

#### Scenario: 点赞一条评论
- **WHEN** 用户对一条未点赞的评论执行点赞操作
- **THEN** 系统记录点赞，评论的 like_count 加 1

#### Scenario: 取消点赞一条评论
- **WHEN** 用户对一条已点赞的评论再次执行点赞操作
- **THEN** 系统取消点赞，评论的 like_count 减 1

# 工具调用链的故障隔离与半开放探测

每次实际 attempt 属于 route 中的一跳。失败阈值与重新检查时间均由本地 policy 给出。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **route**　tenant 的依赖链 primary 及允许替代的 fallback。
- **policy**　service 的 epoch、threshold、recheck_after 和 probe_limit。
- **call**　tenant、id 与已有 checkpoint，可空。
- **step**　id/attempt 对 service/epoch 的实际回执，state 为 invalid、not_reached、timeout、accepted 或 ok。
- **probe**　值班端要求用 id 申请指定 service/epoch 的探测。
- **restart**　重启。
- **inspect**　按 tenant 或 service 查询。

## 示例

- shared-probe-is-still-in-flight.jsonl
- invalid-input-and-old-success.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会按文件统计各类参与方消息的数量。演练记录只描述各参与方交给系统的输入。

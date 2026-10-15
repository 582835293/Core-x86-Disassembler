# 解码器接口与消费者契约

```c
#include "disasm.h"
uint8_t bytes[15] = {0xB8, 0x2A, 0, 0, 0};
DecodeContext decoded;
int length = Disassemble(bytes, 0x401000, &decoded);
/* length == decoded.instr_len == 5; decoded.asm_str 包含 MOV EAX */
```

`ParseInstuction`（沿用原有拼写）解析并填充结构，返回消耗字节数；`FormatInstruction` 把已经解码的结果格式化；`Disassemble` 顺序调用这两步。`instr_len` 与返回值一致，供 TinyX86 计算后继 EIP、CALL 返回地址与相对跳转。`pos` 是解析过程中的游标。

原有 `ParsePrefixes`、`ParseModRM`、`ParseSIB`、`ParseDisplacement`、`ParseImmediate`、`ParseFPU`、`FormatOperand` 和 `FormatModRM` 保持公开，供教学和已有测试使用。它们直接修改调用者初始化的 DecodeContext。

当前高层接口没有输入长度参数，内部使用最多 15 字节的指令读取假设。调用者必须保证对应内存可读；短缓冲区与截断指令的边界检查仍是后续工作。`mode64` 字段不是完整的 64 位模式承诺。

## 共享版本中的修正

- ENTER (`C8 iw ib`) 不再错误关联 MOV 的 Group 11 表。
- 解码成功后写入 `DecodeContext.instr_len`。
- 回归测试同时验证 ENTER 的两个立即数和长度字段，CPU 集成测试在 TinyX86 中链接真实解码库执行。

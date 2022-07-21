# 腾讯云AV1相关

## FLV扩展支持AV1编码格式

扩展说明文档见：[FLV扩展支持AV1编码格式](FLV扩展支持AV1编码格式.md)。

其他参考资料：
1. AV1标准：[https://aomediacodec.github.io/av1-spec/av1-spec.pdf](https://aomediacodec.github.io/av1-spec/av1-spec.pdf)
2. AV1 in MP4：[https://aomediacodec.github.io/av1-isobmff/](https://aomediacodec.github.io/av1-isobmff/)

## FFmpeg FLV定制扩展

Patch文件见 [patch/0002-lavf-flv-add-extensions-for-av1](patch/0002-lavf-flv-add-extensions-for-av1.patch)，使用说明：
1. 因广泛使用FLV扩展支持H.265，同时提供了H.265的patch [patch/0001-lavf-flv-add-extensions-for-h265-hevc.patch](patch/0001-lavf-flv-add-extensions-for-h265-hevc.patch)，使用时应先apply patch/0001，再apply patch/0002。
2. Patch**基于FFmpeg n4.4.2**，其他FFmpeg版本直接打patch可能失败，需参考patch文件做手动修改。

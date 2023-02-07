# 腾讯云FLV扩展

## 扩展说明文档

FLV扩展支持AV1、VVC编码格式说明文档见：[FLV Codec扩展](FLV_Codec.md)。

参考资料：
1. AV1标准：[https://aomediacodec.github.io/av1-spec/av1-spec.pdf](https://aomediacodec.github.io/av1-spec/av1-spec.pdf)
2. AV1 in MP4：[https://aomediacodec.github.io/av1-isobmff/](https://aomediacodec.github.io/av1-isobmff/)
3. VVC/H.266标准：[H.266 : Versatile video coding](https://www.itu.int/rec/T-REC-H.266)
4. VVC in MP4：[ISO/IEC 14496-15:2022
Information technology — Coding of audio-visual objects — Part 15: Carriage of network abstraction layer (NAL) unit structured video in the ISO base media file format](https://www.iso.org/standard/83336.html)

## FFmpeg FLV定制扩展

FFmpeg patch:

- [H.265扩展](patch/0001-lavf-flv-add-extensions-for-h265-hevc.patch)
- [AV1扩展](patch/0002-lavf-flv-add-extensions-for-av1.patch)
- [H.266扩展](patch/0003-lavf-flv-add-extensions-for-vvc.patch)

使用说明：
- Patch**基于FFmpeg n4.4.2**，其他FFmpeg版本直接打patch可能失败，需参考patch文件做手动修改。

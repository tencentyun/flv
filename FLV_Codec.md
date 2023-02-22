## FLV扩展支持AV1、VVC编码格式

| 版本 | 时间 | 更新记录 |
| ------ | ------ | ------ |
| 2.0.0 | 2023-02-07 | 新增VVC编码格式支持 |
| 1.0.0 | 2022-07-21 | 创建 |

以《Adobe Flash Video File Format Specification, Version 10.1》为基础，本文档包含了H.265、AV1和VVC的扩展支持。VIDEODATA部分扩展如下：

### VIDEODATA
The VideoTagHeader contains video-specific metadata.
#### VideoTagHeader
|       Field       | Type                                                                                                                                                                                                                                                                                                                                                                                                         | Comment                                                                                                                                                                                                                                                                                                                                                                                  |
|:-----------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Frame Type     | UB [4]                                                                                                                                                                                                                                                                                                                                                                                                       | Frame Type Type of video frame. The following values are defined: <br>1 = key frame (for AVC and HEVC, a seekable frame)<br>2 = inter frame (for AVC and HEVC, a non-seekable frame)<br>3 = disposable inter frame (H.263 only)<br>4 = generated key frame (reserved for server use only)<br>5 = video info/command frame                                                                |
|      CodecID      | UB [4]                                                                                                                                                                                                                                                                                                                                                                                                       | Codec Identifier. The following values are defined:<br>2 = Sorenson H.263<br>3 = Screen video<br>4 = On2 VP6<br>5 = On2 VP6 with alpha channel<br>6 = Screen video version 2<br>7 = AVC<br/>**12 = HEVC**<br>**13 = AV1**<br>**14 = VVC**                                                                                                                                                |
| **HVCPacketType** | IF CodecID == 12<br> UI8                                                                                                                                                                                                                                                                                                                                                                                     | The following values are defined:<br>0 = HEVC sequence header<br>1 = HEVC NALU<br>2 = HEVC end of sequence (lower level NALU sequence ender is not required or supported                                                                                                                                                                                                                 |
| **AV1PacketType** | IF CodecID == 13<br> UI8                                                                                                                                                                                                                                                                                                                                                                                     | The following values are defined:<br>0: AV1CodecConfigurationRecord<br>1: OBU                                                                                                                                                                                                                                                                                                            |
| **VVCPacketType** | IF CodecID == 14<br> UI8                                                                                                                                                                                                                                                                                                                                                                                     | The following values are defined:<br>0: VvcDecoderConfigurationRecord<br>1: VVC NALU                                                                                                                                                                                                                                                                                                     |
|  CompositionTime  | IF CodecID == 7 OR CodecID == 12 OR CodecID == 13 OR CodecID == 14 <br>SI24                                                                                                                                                                                                                                                                                                                                  | IF AVCPacketType == 1 OR HVCPacketType == 1 OR AV1PacketType == 1 OR VVCPacketType == 1<br>Composition time offset<br>ELSE<br>0<br>See ISO 14496-12, 8.15.3 for an explanation of composition times. The offset in an FLV file is always in milliseconds.                                                                                                                                |
|   VideoTagBody    | IF FrameType == 5<br>  UI8<br>ELSE (<br>IF CodecID == 2<br>H263VIDEOPACKET<br>IF CodecID == 3<br>SCREENVIDEOPACKET<br>IF CodecID == 4<br>VP6FLVVIDEOPACKET<br>IF CodecID == 5<br>VP6FLVALPHAVIDEOPACKET<br>IF CodecID == 6<br>SCREENV2VIDEOPACKET<br>IF CodecID == 7 <br>AVCVIDEOPACKET<br>IF CodecID == 12 <br>HVCVIDEOPACKET<br>IF CodecID == 13 <br>AV1VIDEOPACKET<br>IF CodecID == 14<br>VVCVIDEOPACKET) | Video frame payload or frame info<br>If FrameType == 5, instead of a video payload, the Video Data Body contains a UI8 with the following meaning:<br>0 = Start of client-side seeking video frame sequence<br>1 = End of client-side seeking video frame sequence<br>For all but AVCVIDEOPACKET, HVCVIDEOPACKET or AV1VIDEOPACKET, see the SWF File<br>Format Specification for details |

#### AV1

AV1CodecConfigurationRecord 的定义见：[https://aomediacodec.github.io/av1-isobmff/](https://aomediacodec.github.io/av1-isobmff/)，摘录如下：

```
aligned (8) class AV1CodecConfigurationRecord {
  unsigned int (1) marker = 1;
  unsigned int (7) version = 1;
  unsigned int (3) seq_profile;
  unsigned int (5) seq_level_idx_0;
  unsigned int (1) seq_tier_0;
  unsigned int (1) high_bitdepth;
  unsigned int (1) twelve_bit;
  unsigned int (1) monochrome;
  unsigned int (1) chroma_subsampling_x;
  unsigned int (1) chroma_subsampling_y;
  unsigned int (2) chroma_sample_position;
  unsigned int (3) reserved = 0;

  unsigned int (1) initial_presentation_delay_present;
  if (initial_presentation_delay_present) {
    unsigned int (4) initial_presentation_delay_minus_one;
  } else {
    unsigned int (4) reserved = 0;
  }

  unsigned int (8) configOBUs[];
}
```

AV1 OBU定义见AV1标准：[https://aomediacodec.github.io/av1-spec/av1-spec.pdf](https://aomediacodec.github.io/av1-spec/av1-spec.pdf)

AV1 bit stream有两种格式：
* Low overhead bit stream format        // 低开销bit stream格式
* Length delimited bit stream format    // 长度分割bit stream格式

目前默认支持low overhead bitstream格式OBU，length delimited bit stream格式会造成一定浪费，因此建议用low overhead格式。

#### VVC

VvcDecoderConfigurationRecord 的定义见：[ISO/IEC 14496-15:2022
Information technology — Coding of audio-visual objects — Part 15: Carriage of network abstraction layer (NAL) unit structured video in the ISO base media file format](https://www.iso.org/standard/83336.html)，摘录如下：

```
aligned(8) class VvcPTLRecord(num_sublayers) {
	bit(2) reserved = 0;
	unsigned int(6) num_bytes_constraint_info;
	unsigned int(7) general_profile_idc;
	unsigned int(1) general_tier_flag;
	unsigned int(8) general_level_idc;
	unsigned int(1) ptl_frame_only_constraint_flag;
	unsigned int(1) ptl_multi_layer_enabled_flag;
	unsigned int(8*num_bytes_constraint_info - 2) general_constraint_info;
	for (i=num_sublayers - 2; i >= 0; i--)
		unsigned int(1) ptl_sublayer_level_present_flag[i];
	for (j=num_sublayers; j<=8 && num_sublayers > 1; j++)
		bit(1) ptl_reserved_zero_bit = 0;
	for (i=num_sublayers-2; i >= 0; i--)
		if (ptl_sublayer_level_present_flag[i])
			unsigned int(8) sublayer_level_idc[i];
	unsigned int(8) ptl_num_sub_profiles;
	for (j=0; j < ptl_num_sub_profiles; j++)
		unsigned int(32) general_sub_profile_idc[j];
}

aligned(8) class VvcDecoderConfigurationRecord {
	bit(5) reserved = '11111'b;
	unsigned int(2) LengthSizeMinusOne;
	unsigned int(1) ptl_present_flag;
	if (ptl_present_flag) {
		unsigned int(9) ols_idx;
		unsigned int(3) num_sublayers;
		unsigned int(2) constant_frame_rate;
		unsigned int(2) chroma_format_idc;
		unsigned int(3) bit_depth_minus8;
		bit(5) reserved = '11111'b;
		VvcPTLRecord(num_sublayers) native_ptl;
		unsigned_int(16) max_picture_width;
		unsigned_int(16) max_picture_height;
		unsigned int(16) avg_frame_rate;
	}
	unsigned int(8) num_of_arrays;
	for (j=0; j < num_of_arrays; j++) {
		unsigned int(1) array_completeness;
		bit(2) reserved = 0;
		unsigned int(5) NAL_unit_type;
		if (NAL_unit_type != DCI_NUT  &&  NAL_unit_type != OPI_NUT)
			unsigned int(16) num_nalus;
		for (i=0; i< num_nalus; i++) {
			unsigned int(16) nal_unit_length;
			bit(8*nal_unit_length) nal_unit;
		}
	}
}
```
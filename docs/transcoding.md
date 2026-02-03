# MediaCMSのトランスコーディング

MediaCMSはメディアファイルのトランスコーディングにFFmpegを使用します。トランスコーディングの設定と構成のほとんどは`files/helpers.py`で定義されています。

## 設定オプション

いくつかのトランスコーディングパラメータは`cms/settings.py`でカスタマイズできます：

### FFmpegプリセット

デフォルトのFFmpegプリセットは「medium」に設定されています。この設定は、エンコード速度と圧縮効率のトレードオフを制御します。

```python
# ffmpeg options
FFMPEG_DEFAULT_PRESET = "medium" # see https://trac.ffmpeg.org/wiki/Encode/H.264
```

利用可能なプリセットには以下が含まれます：
- ultrafast
- superfast
- veryfast
- faster
- fast
- medium（デフォルト）
- slow
- slower
- veryslow

より高速なプリセットは同じ品質でより大きなファイルサイズになり、より遅いプリセットはより良い圧縮を提供しますが、エンコードに時間がかかります。

### その他のトランスコーディング設定

`settings.py`の追加のトランスコーディング設定には以下が含まれます：

- `FFMPEG_COMMAND`: FFmpeg実行可能ファイルへのパス
- `FFPROBE_COMMAND`: FFprobe実行可能ファイルへのパス
- `DO_NOT_TRANSCODE_VIDEO`: Trueに設定すると、トランスコードせずに元の動画のみが表示されます
- `CHUNKIZE_VIDEO_DURATION`: この期間（秒単位）より長い動画は、チャンクに分割され、個別にエンコードされます
- `VIDEO_CHUNKS_DURATION`: 各チャンクの期間（CHUNKIZE_VIDEO_DURATIONより小さくする必要があります）
- `MINIMUM_RESOLUTIONS_TO_ENCODE`: アップスケーリングが必要な場合でも、常にこれらの解像度をエンコードします

## 高度な設定

より高度なトランスコーディング設定については、`files/helpers.py`で次を変更する必要がある場合があります：

- さまざまなコーデックと解像度の動画ビットレート
- オーディオエンコーダーとビットレート
- CRF（Constant Rate Factor）値
- キーフレーム設定
- さまざまなコーデック（H.264、H.265、VP9）のエンコードパラメータ

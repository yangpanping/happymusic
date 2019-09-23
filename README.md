# happymusic

## 简介
波尼音乐是一款开源Android在线音乐播放器。
- 播放本地音乐与在线音乐
- 在线音乐排行榜，如热歌榜、新歌榜等
- 高仿云音乐的黑胶唱片专辑封面
- 歌词显示，自动搜索歌词
- 编辑歌曲信息
- 夜间模式
- 定时关闭
-歌词支持上下拖动
- 支持分屏模式
- 本地歌曲支持按大小和时长过滤
- 下载的歌曲文件自动添加专辑封面
- 编辑歌曲信息
- 5.0以上系统支持联动系统媒体中心，锁屏显示播放信息
- 修复已知bug
- 新增通知栏播放控制

- 下载在线歌曲可以显示专辑封面了

## TODO
- [x] 在线音乐可以免下载加入我的音乐列表
- [ ] 在线音乐自动缓存
- [x] 编辑音乐信息

## 项目
### 公开API
- 在线音乐：[百度音乐](http://mrasong.com/a/baidu-mp3-api-full)
- 天气数据：[高德地图](http://lbs.amap.com/)

### 开源技术
- [okhttp-utils](https://github.com/hongyangAndroid/okhttp-utils)
- [Glide](https://github.com/bumptech/glide)

### 关键代码
黑胶唱片专辑封面绘制流程
```
@Override
protected void onDraw(Canvas canvas) {
    // 1.绘制顶部虚线
    mTopLine.setBounds(0, 0, getWidth(), mTopLineHeight);
    mTopLine.draw(canvas);
    // 2.绘制黑胶唱片外侧半透明边框
    mCoverBorder.setBounds(mDiscPoint.x - mCoverBorderWidth, mDiscPoint.y - mCoverBorderWidth,
            mDiscPoint.x + mDiscBitmap.getWidth() + mCoverBorderWidth, mDiscPoint.y +
                    mDiscBitmap.getHeight() + mCoverBorderWidth);
    mCoverBorder.draw(canvas);
    // 3.绘制黑胶
    // 设置旋转中心和旋转角度，setRotate和preTranslate顺序很重要
    mDiscMatrix.setRotate(mDiscRotation, mDiscCenterPoint.x, mDiscCenterPoint.y);
    // 设置图片起始坐标
    mDiscMatrix.preTranslate(mDiscPoint.x, mDiscPoint.y);
    canvas.drawBitmap(mDiscBitmap, mDiscMatrix, null);
    // 4.绘制封面
    mCoverMatrix.setRotate(mDiscRotation, mCoverCenterPoint.x, mCoverCenterPoint.y);
    mCoverMatrix.preTranslate(mCoverPoint.x, mCoverPoint.y);
    canvas.drawBitmap(mCoverBitmap, mCoverMatrix, null);
    // 5.绘制指针
    mNeedleMatrix.setRotate(mNeedleRotation, mNeedleCenterPoint.x, mNeedleCenterPoint.y);
    mNeedleMatrix.preTranslate(mNeedlePoint.x, mNeedlePoint.y);
    canvas.drawBitmap(mNeedleBitmap, mNeedleMatrix, null);
}
```
歌词绘制流程
```
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    // 中心Y坐标
    float centerY = getHeight() / 2 + mTextSize / 2 + mAnimOffset;

    // 无歌词文件
    if (!hasLrc()) {
        float centerX = (getWidth() - mCurrentPaint.measureText(label)) / 2;
        canvas.drawText(label, centerX, centerY, mCurrentPaint);
        return;
    }

    // 画当前行
    String currStr = mLrcTexts.get(mCurrentLine);
    float currX = (getWidth() - mCurrentPaint.measureText(currStr)) / 2;
    canvas.drawText(currStr, currX, centerY, mCurrentPaint);

    // 画当前行上面的
    for (int i = mCurrentLine - 1; i >= 0; i--) {
        String upStr = mLrcTexts.get(i);
        float upX = (getWidth() - mNormalPaint.measureText(upStr)) / 2;
        float upY = centerY - (mTextSize + mDividerHeight) * (mCurrentLine - i);
        // 超出屏幕停止绘制
        if (upY - mTextSize < 0) {
            break;
        }
        canvas.drawText(upStr, upX, upY, mNormalPaint);
    }

    // 画当前行下面的
    for (int i = mCurrentLine + 1; i < mLrcTimes.size(); i++) {
        String downStr = mLrcTexts.get(i);
        float downX = (getWidth() - mNormalPaint.measureText(downStr)) / 2;
        float downY = centerY + (mTextSize + mDividerHeight) * (i - mCurrentLine);
        // 超出屏幕停止绘制
        if (downY > getHeight()) {
            break;
        }
        canvas.drawText(downStr, downX, downY, mNormalPaint);
    }
}
```





# opencv.js-wechat
WeChat Mini-program lib for opencv.js

opencv.js版本为4.3.0

通过webAssembly执行

## 示例

### 小程序

将demo\miniprogram里的项目导入到开发者工具，点击预览，手机上打开调试后，先点击"获取wasm"，约5-10秒初始化完成。

1. 点击"显示图片"，查看是否正常绘制了Lena图片。
2. 点击"灰度lena"，查看是否正常显示灰色的Lena图片。
3. 点击"捕获相机"，约5-10秒加载完成后，查看是否正常追踪面部（红框）以及眼睛（蓝框）。

### 服务端

在demo\server里执行"npx http-server ./"，在浏览器中打开"http://localhost:8080/tests.html"，查看是否通过测试。

## API

### wasm

```
wasm.init({
	url:"http://www.aiotforest.com/opencv.zip", // wasm或zip文件网址
	type:"zip", // 格式：wasm,zip（仅压缩了wasm的压缩包）
	useCache:true, // 是否使用缓存（开启缓存会将wasm存入用户本地文件，节省下次请求流量）
	self:this, // 传入全局环境
	success: function (Module) {// Module为opencv对象
	}
})
```

### opencv

#### 注意

要在本地设置里取消勾选```ES6转ES5```

因为小程序没有dom，wx.createOffscreenCanvas不咋好用等原因，进行了一些魔改。

1. 如对无法使用的new Function，用固定的function加动态获取传入参数仿写
2. 对某些函数读取外部文件，将FS改造，提前将文件通过小程序的FileSystemManager读入虚拟目录中
3. imread

```
cv.imread(imageSource, callback)
```

imageSource 可以是canvas的id，也可以是以http开头的图片地址。如果是图片地址，则需要存在一个id为#OffscreenCanvas的canvas用来中转（建议隐藏该元素）。

callback 是回调函数

4. imshow

```
cv.imshow(canvasSource, mat, contextSource)
```

canvasSource 是输出canvas的id 或者canvas对象

mat 是要显示的图

contextSource 是输出context的id 或者context对象



具体请访问[opencv.js docs](https://docs.opencv.org/4.3.0/d5/d10/tutorial_js_root.html)

server中编译的wasm配置包含以下参数：
```
core = {'': ['absdiff', 'add', 'addWeighted', 'bitwise_and', 'bitwise_not', 'bitwise_or', 'bitwise_xor', 'cartToPolar',\
             'compare', 'convertScaleAbs', 'copyMakeBorder', 'countNonZero', 'determinant', 'dft', 'divide', 'eigen', \
             'exp', 'flip', 'getOptimalDFTSize','gemm', 'hconcat', 'inRange', 'invert', 'kmeans', 'log', 'magnitude', \
             'max', 'mean', 'meanStdDev', 'merge', 'min', 'minMaxLoc', 'mixChannels', 'multiply', 'norm', 'normalize', \
             'perspectiveTransform', 'polarToCart', 'pow', 'randn', 'randu', 'reduce', 'repeat', 'rotate', 'setIdentity', 'setRNGSeed', \
             'solve', 'solvePoly', 'split', 'sqrt', 'subtract', 'trace', 'transform', 'transpose', 'vconcat'],
        'Algorithm': []}

imgproc = {'': ['Canny', 'GaussianBlur', 'Laplacian', 'HoughLines', 'HoughLinesP', 'HoughCircles', 'Scharr','Sobel', \
                'adaptiveThreshold','approxPolyDP','arcLength','bilateralFilter','blur','boundingRect','boxFilter',\
                'calcBackProject','calcHist','circle','compareHist','connectedComponents','connectedComponentsWithStats', \
                'contourArea', 'convexHull', 'convexityDefects', 'cornerHarris','cornerMinEigenVal','createCLAHE', \
                'createLineSegmentDetector','cvtColor','demosaicing','dilate', 'distanceTransform','distanceTransformWithLabels', \
                'drawContours','ellipse','ellipse2Poly','equalizeHist','erode', 'filter2D', 'findContours','fitEllipse', \
                'fitLine', 'floodFill','getAffineTransform', 'getPerspectiveTransform', 'getRotationMatrix2D', 'getStructuringElement', \
                'goodFeaturesToTrack','grabCut','initUndistortRectifyMap', 'integral','integral2', 'isContourConvex', 'line', \
                'matchShapes', 'matchTemplate','medianBlur', 'minAreaRect', 'minEnclosingCircle', 'moments', 'morphologyEx', \
                'pointPolygonTest', 'putText','pyrDown','pyrUp','rectangle','remap', 'resize','sepFilter2D','threshold', \
                'undistort','warpAffine','warpPerspective','warpPolar','watershed', \
                'fillPoly', 'fillConvexPoly'],
           'CLAHE': ['apply', 'collectGarbage', 'getClipLimit', 'getTilesGridSize', 'setClipLimit', 'setTilesGridSize']}

objdetect = {'': ['groupRectangles'],
             'HOGDescriptor': ['load', 'HOGDescriptor', 'getDefaultPeopleDetector', 'getDaimlerPeopleDetector', 'setSVMDetector', 'detectMultiScale'],
             'CascadeClassifier': ['load', 'detectMultiScale2', 'CascadeClassifier', 'detectMultiScale3', 'empty', 'detectMultiScale']}

video = {'': ['CamShift', 'calcOpticalFlowFarneback', 'calcOpticalFlowPyrLK', 'createBackgroundSubtractorMOG2', \
             'findTransformECC', 'meanShift'],
         'BackgroundSubtractorMOG2': ['BackgroundSubtractorMOG2', 'apply'],
         'BackgroundSubtractor': ['apply', 'getBackgroundImage']}

dnn = {'dnn_Net': ['setInput', 'forward'],
       '': ['readNetFromCaffe', 'readNetFromTensorflow', 'readNetFromTorch', 'readNetFromDarknet',
            'readNetFromONNX', 'readNet', 'blobFromImage']}

features2d = {'Feature2D': ['detect', 'compute', 'detectAndCompute', 'descriptorSize', 'descriptorType', 'defaultNorm', 'empty', 'getDefaultName'],
              'BRISK': ['create', 'getDefaultName'],
              'ORB': ['create', 'setMaxFeatures', 'setScaleFactor', 'setNLevels', 'setEdgeThreshold', 'setFirstLevel', 'setWTA_K', 'setScoreType', 'setPatchSize', 'getFastThreshold', 'getDefaultName'],
              'MSER': ['create', 'detectRegions', 'setDelta', 'getDelta', 'setMinArea', 'getMinArea', 'setMaxArea', 'getMaxArea', 'setPass2Only', 'getPass2Only', 'getDefaultName'],
              'FastFeatureDetector': ['create', 'setThreshold', 'getThreshold', 'setNonmaxSuppression', 'getNonmaxSuppression', 'setType', 'getType', 'getDefaultName'],
              'AgastFeatureDetector': ['create', 'setThreshold', 'getThreshold', 'setNonmaxSuppression', 'getNonmaxSuppression', 'setType', 'getType', 'getDefaultName'],
              'GFTTDetector': ['create', 'setMaxFeatures', 'getMaxFeatures', 'setQualityLevel', 'getQualityLevel', 'setMinDistance', 'getMinDistance', 'setBlockSize', 'getBlockSize', 'setHarrisDetector', 'getHarrisDetector', 'setK', 'getK', 'getDefaultName'],
              # 'SimpleBlobDetector': ['create'],
              'KAZE': ['create', 'setExtended', 'getExtended', 'setUpright', 'getUpright', 'setThreshold', 'getThreshold', 'setNOctaves', 'getNOctaves', 'setNOctaveLayers', 'getNOctaveLayers', 'setDiffusivity', 'getDiffusivity', 'getDefaultName'],
              'AKAZE': ['create', 'setDescriptorType', 'getDescriptorType', 'setDescriptorSize', 'getDescriptorSize', 'setDescriptorChannels', 'getDescriptorChannels', 'setThreshold', 'getThreshold', 'setNOctaves', 'getNOctaves', 'setNOctaveLayers', 'getNOctaveLayers', 'setDiffusivity', 'getDiffusivity', 'getDefaultName'],
              'DescriptorMatcher': ['add', 'clear', 'empty', 'isMaskSupported', 'train', 'match', 'knnMatch', 'radiusMatch', 'clone', 'create'],
              'BFMatcher': ['isMaskSupported', 'create'],
              '': ['drawKeypoints', 'drawMatches', 'drawMatchesKnn']}

photo = {'': ['createAlignMTB', 'createCalibrateDebevec', 'createCalibrateRobertson', \
              'createMergeDebevec', 'createMergeMertens', 'createMergeRobertson', \
              'createTonemapDrago', 'createTonemapMantiuk', 'createTonemapReinhard', 'inpaint'],
        'CalibrateCRF': ['process'],
        'AlignMTB' : ['calculateShift', 'shiftMat', 'computeBitmaps', 'getMaxBits', 'setMaxBits', \
                      'getExcludeRange', 'setExcludeRange', 'getCut', 'setCut'],
        'CalibrateDebevec' : ['getLambda', 'setLambda', 'getSamples', 'setSamples', 'getRandom', 'setRandom'],
        'CalibrateRobertson' : ['getMaxIter', 'setMaxIter', 'getThreshold', 'setThreshold', 'getRadiance'],
        'MergeExposures' : ['process'],
        'MergeDebevec' : ['process'],
        'MergeMertens' : ['process', 'getContrastWeight', 'setContrastWeight', 'getSaturationWeight', \
                          'setSaturationWeight', 'getExposureWeight', 'setExposureWeight'],
        'MergeRobertson' : ['process'],
        'Tonemap' : ['process' , 'getGamma', 'setGamma'],
        'TonemapDrago' : ['getSaturation', 'setSaturation', 'getBias', 'setBias', \
                          'getSigmaColor', 'setSigmaColor', 'getSigmaSpace','setSigmaSpace'],
        'TonemapMantiuk' : ['getScale', 'setScale', 'getSaturation', 'setSaturation'],
        'TonemapReinhard' : ['getIntensity', 'setIntensity', 'getLightAdaptation', 'setLightAdaptation', \
                             'getColorAdaptation', 'setColorAdaptation']
        }

aruco = {'': ['detectMarkers', 'drawDetectedMarkers', 'drawAxis', 'estimatePoseSingleMarkers', 'estimatePoseBoard', 'estimatePoseCharucoBoard', 'interpolateCornersCharuco', 'drawDetectedCornersCharuco'],
        'aruco_Dictionary': ['get', 'drawMarker'],
        'aruco_Board': ['create'],
        'aruco_GridBoard': ['create', 'draw'],
        'aruco_CharucoBoard': ['create', 'draw'],
        }

calib3d = {'': ['findHomography', 'calibrateCameraExtended', 'drawFrameAxes', 'estimateAffine2D', 'getDefaultNewCameraMatrix', 'initUndistortRectifyMap', 'Rodrigues']}


white_list = makeWhiteList([core, imgproc, objdetect, video, dnn, features2d, photo, aruco, calib3d])

```

## 其他

Fork自[leo9960](https://github.com/leo9960)/[opencv.js-wechat](https://github.com/leo9960/opencv.js-wechat)

这位大佬似乎只想给大家提供个思路，无心维护这个项目。

刚开始代码拿到手上也不咋能用，也有一些bug，我就根据自己的理解做了一些修改，希望能让它的门槛更低，可以开箱即用。但是因为水平实在有限，如果有什么不对的地方，希望能不吝赐教。
# PassioAPI 

## Version  2.2.11
```kotlin
typealias PassioID = String

data class ClassificationCandidate(
    val label: LabelMetadata,
    val confidence: Float)

data class ObjectDetectionCandidate(
    var passioID: PassioID,
    var name: String,
    var confidence: Float,
    val boundingBox: RectF
)

interface ClassificationListener {
    fun onClassificationResult(candidate: ClassificationCandidate, bitmap: Bitmap)
}

interface ObjectDetectionListener {
    fun onObjectDetectionResult(candidates: List<ObjectDetectionCandidate>, bitmap: Bitmap)
}

private const val MINIMUM_CONFIDENCE_TF_OD_API = 0.33f

interface PassioSDK {

    companion object {
        private var internalInstance: PassioSDK? = null

        /**
         * Use this singleton to access the Passio SDK.
         */
        @JvmStatic
        val instance: PassioSDK
            get() {
                if (internalInstance == null) {
                    internalInstance = PassioSDKImpl()
                }
                return internalInstance!!
            }

        /**
         * The value of confidence of recognition results below which results won't be returned
         * to the user. The default value is 0.5.
         */
        fun minimumConfidence() = MINIMUM_CONFIDENCE_TF_OD_API
    }
    
  
    fun configure(
        passioConfiguration: PassioConfiguration,
        onComplete: (status: PassioStatus) -> Unit
    )


    /**
     * Starts the camera preview with the given [PassioCameraViewProvider]. Using CameraX
     * (https://developer.android.com/training/camerax), the camera system will start rendering
     * the frames onto the PreviewView. Also this method binds the camera with the lifecycle
     * provider of the CameraViewProvider. When that lifecycle provider calls onStart() the
     * camera preview will start and when it calls onStop() the camera preview will stop and
     * start the shutdown process. [android.view.Surface.ROTATION_]
     *
     * @param passioCameraViewProvider provides the PreviewView to render the camera frames, the
     *        lifecycle holder which will start and stop the camera, and also the context provider
     *        needed to open the camera.
     * @param displayRotation if the orientation changes are enabled, use this parameter to notify
     *        the camera of the display rotation. Accepted values are: Surface.ROTATION_0,
     *        Surface.ROTATION_90, Surface.ROTATION_180, Surface.ROTATION_270.
     * @param tapToFocus set true to enable a tap listener on the passioCameraViewProvider's
     *        PreviewView that will start a manual focus operation.
     */
    fun startCamera(
        passioCameraViewProvider: PassioCameraViewProvider,
        displayRotation: Int = 0,
        cameraFacing: PassioCameraViewProvider.CameraFacing = PassioCameraViewProvider.CameraFacing.BACK,
        tapToFocus: Boolean = false,
    )


    /**
     * Turns the device's flashlight on or off.
     *
     * @param enabled if enabled is set to true, it will turn the flashlight on. If the enabled
     *        parameter is the to false, it will turn the flashlight off.
     */
    fun enableFlashlight(enabled: Boolean)


    /**
     * Finalizes the PassioSDK instance. Deallocates the memory reserved for TFLite models,
     * transformation matrices and shuts down all the background queues that power the SDK. To run
     * the SDK again, [configure] must be called.
     */
    fun shutDownPassioSDK() {
        internalInstance = null
    }

    /**
     * If not null, the [PassioStatusListener] will provide callbacks
     * when the internal state of the SDK's configuration process changes.
     * Passing null will unregister the listener.
     */
    fun setPassioStatusListener(statusListener: PassioStatusListener?)

    fun startDetection(listener: ClassificationListener)

    fun stopDetection()

    fun getInstalledVersion(context: Context, projectId: String?): Int

    fun getSDKVersion(): String
}
```
<sup>Copyright 2022 Passio Inc</sup>

Backend                       infer p50   e2e p50   e2e p95     FPS  persons/frame

\----------------------------------------------------------------------------------

PyTorch (CPU)                    27.7ms    29.6ms    54.8ms    29.8           2.30

OpenVINO FP32 (intel:cpu)        29.2ms    32.5ms    64.7ms    27.7           2.45



The two backends aren’t processing the same image size.



PyTorch shrinks a 16:9 frame to 640 wide and pads it only to the next multiple of 32, so the model sees about 384×640.

The OpenVINO export has a fixed 640×640 input, so every frame is padded to a square. That’s about 67% more pixels per frame.



The fix: IP cameras send a fixed resolution, so the right deployment choice is to export at the camera’s shape, which is a fixed rectangular input. Then both backends get exactly the same input. I’ll update the script.



**After Fix:**



Backend                       infer p50   e2e p50   e2e p95     FPS  persons/frame

\----------------------------------------------------------------------------------

PyTorch (CPU)                    33.6ms    36.0ms    63.4ms    25.1           2.30

OpenVINO FP32 (intel:cpu)        19.0ms    21.7ms    36.8ms    41.1           2.30



Backend                       infer p50   e2e p50   e2e p95     FPS  persons/frame

\----------------------------------------------------------------------------------

PyTorch (CPU)                    26.4ms    28.4ms    41.9ms    33.2           2.30

OpenVINO FP32 (intel:gpu)         4.2ms     8.1ms    13.7ms   108.7           2.30


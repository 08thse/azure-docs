## Integration of Log Sharing Feature Using CallingSDK and SSZipArchive

In this tutorial, we guide you through the process of integrating a log sharing feature into an iOS application. Once complete you'll be able to collect the log files into a Zip File, and use the iOS built in ShareSheet to allow exporting from the device.

### Prerequisites

Ensure that you have:

- A working integration of CallingSDK in your application.
- Access to a `CallClient` object.
- `SSZipArchive` for creating ZIP archives on the device.

### Steps

#### Step 1: Define Required Variables

Declare the following variables in your SwiftUI View:

```swift
@State private var isSharing = false
let zipFilePath = URL(fileURLWithPath: NSTemporaryDirectory()).appendingPathComponent("logs.zip")
```

`isSharing` is a state variable that is used to control the visibility of the sharing sheet in SwiftUI. 
The `zipFilePath` is the path where the ZIP file containing the logs is stored in a temporary directory.

### Step 2: Add a Button to Trigger Log Sharing

Add the following SwiftUI Button and sheet in your View:

```swift
Button("Share Logs") {
    shareLogs()
    self.isSharing = true
}
.sheet(isPresented: $isSharing) {
    VStack {
        Text("Share the log files")
        ShareSheet(items: [zipFilePath])
    }
}
```

In the above code, when the button is clicked, the `shareLogs()` function is called and `isSharing` is set to `true`. This binding enables the presentation of a sheet containing a `ShareSheet` populated with the ZIP file containing the logs.

#### Step 3: Implement the Log Dumping Function

Next, define the `shareLogs()` function:

```swift
func shareLogs() {
    if let files = callClient.debugInfo.getSupportFiles() {
        let success = SSZipArchive.createZipFile(atPath: zipFilePath.path, withFilesAtPaths: files.map { $0.path })
        if success {
            DispatchQueue.main.async {
                isSharing = true
            }
        } else {
            print("Error creating ZIP archive")
        }
    }
}
```

Here, `getSupportFiles()` is called from `debugInfo` of the `CallClient` object to fetch the log files. The `SSZipArchive.createZipFile` method is then used to create a ZIP archive at `zipFilePath`. If the zipping operation succeeds, `isSharing` is set to `true` on the main thread, thus triggering the display of the sharing sheet. If an error occurs, a message is printed to the console.

### Conclusion

You have now integrated a basic log sharing feature in your iOS application. This feature enables users to easily share logs directly from the application, which can be crucial for debugging and support.


Peter Steinberger08-Jun-2016 09:29 AM

This is resolved. Thank you!
This issue has been verified as resolved and can be closed.
Apple Developer Relations21-Mar-2016 08:36 PM

Please test this issue with the latest Xcode GM release.
	
The GM release includes the Xcode IDE, Swift compiler, Instruments, Simulator, and latest SDKs for OS X, iOS, tvOS and watchOS.
	
Please test with this release.  If you still have issues, please provide any relevant logs or information that could help us investigate.

Xcode
https://developer.apple.com/xcode/download/
Peter Steinberger28-Oct-2015 02:30 AM

See https://twitter.com/johannesweiss/status/659175536556703744 and conversations.
Peter Steinberger28-Oct-2015 01:57 AM

Update with reduced test case and found a workaround. Add __nullable to the block return parameter.

'SwiftLambdaBlock.zip' and 'SwiftLambdaBlock.zip' were successfully uploaded.
Peter Steinberger28-Oct-2015 01:02 AM

Copy of the sample attached. AppDelegate.m runs the API in ObjC (works) then SwiftTest.swift does the same in Swift (crash)

'SwiftLambdaBlock.zip' was successfully uploaded.
Peter Steinberger28-Oct-2015 00:10 AM

Trying @convention(block) didn't change anything either.

        let getPassphrase: @convention(block) () -> String = {
            print("called!")
            return NSString(string: "test") as String
        }

        let cryptoWrapper = PSPDFAESCryptoDataProvider(URL: encryptedPDF, passphraseProvider: getPassphrase, salt: salt, rounds: PSPDFDefaultPBKDFNumberOfRounds)!

Calling the same code from Objective-C of course works. I've updated the dropbox example.
Peter Steinberger27-Oct-2015 11:57 PM

Summary:
We see a crash when calling a Swift lambda from within ObjC. The same code works fine in an isolated example.

Steps to Reproduce:
Run app. Observe crash: (EXC_i386_GPFLT)

(lldb) bt
* thread #1: tid = 0x46564d, 0x000000010728a80b libobjc.A.dylib`objc_msgSend + 11, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=EXC_I386_GPFLT)
    frame #0: 0x000000010728a80b libobjc.A.dylib`objc_msgSend + 11
    frame #1: 0x00000001060923a8 PSPDFKit`___lldb_unnamed_function1406$$PSPDFKit + 834
    frame #2: 0x0000000105da72a3 SwiftLambdaBlock`@nonobjc __ObjC.PSPDFAESCryptoDataProvider.init (__ObjC.PSPDFAESCryptoDataProvider.Type)(URL : __ObjC.NSURL, passphraseProvider : () -> Swift.String, salt : Swift.String, rounds : Swift.UInt) -> Swift.Optional<__ObjC.PSPDFAESCryptoDataProvider> + 195 at SwiftTest.swift:0
    frame #3: 0x0000000105da6c5d SwiftLambdaBlock`__ObjC.PSPDFAESCryptoDataProvider.__allocating_init (__ObjC.PSPDFAESCryptoDataProvider.Type)(URL : __ObjC.NSURL, passphraseProvider : () -> Swift.String, salt : Swift.String, rounds : Swift.UInt) -> Swift.Optional<__ObjC.PSPDFAESCryptoDataProvider> + 141 at SwiftTest.swift:0
  * frame #4: 0x0000000105da63d9 SwiftLambdaBlock`SwiftLambdaBlock.SwiftTest.init (self=0x00007f9d38cc8780)() -> SwiftLambdaBlock.SwiftTest + 1097 at SwiftTest.swift:31
    frame #5: 0x0000000105da6761 SwiftLambdaBlock`@objc SwiftLambdaBlock.SwiftTest.init (SwiftLambdaBlock.SwiftTest.Type)() -> SwiftLambdaBlock.SwiftTest + 17 at SwiftTest.swift:0
    frame #6: 0x0000000105da5dfb SwiftLambdaBlock`-[AppDelegate application:didFinishLaunchingWithOptions:](self=0x00007f9d38c33f50, _cmd="application:didFinishLaunchingWithOptions:", application=0x00007f9d38c317f0, launchOptions=0x0000000000000000) + 139 at AppDelegate.m:20
    frame #7: 0x0000000107c48f24 UIKit`-[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:] + 272
    frame #8: 0x0000000107c4a0ca UIKit`-[UIApplication _callInitializationDelegatesForMainScene:transitionContext:] + 3415
    frame #9: 0x0000000107c509c3 UIKit`-[UIApplication _runWithMainScene:transitionContext:completion:] + 1750
    frame #10: 0x0000000107c4dba3 UIKit`-[UIApplication workspaceDidEndTransaction:] + 188
    frame #11: 0x0000000111402784 FrontBoardServices`-[FBSSerialQueue _performNext] + 192
    frame #12: 0x0000000111402af2 FrontBoardServices`-[FBSSerialQueue _performNextFromRunLoopSource] + 45
    frame #13: 0x00000001077d1011 CoreFoundation`__CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__ + 17
    frame #14: 0x00000001077c6f3c CoreFoundation`__CFRunLoopDoSources0 + 556
    frame #15: 0x00000001077c63f3 CoreFoundation`__CFRunLoopRun + 867
    frame #16: 0x00000001077c5e08 CoreFoundation`CFRunLoopRunSpecific + 488
    frame #17: 0x0000000107c4d4f5 UIKit`-[UIApplication _run] + 402
    frame #18: 0x0000000107c5230d UIKit`UIApplicationMain + 171
    frame #19: 0x0000000105da5f5f SwiftLambdaBlock`main(argc=1, argv=0x00007fff59e5a670) + 111 at main.m:14
    frame #20: 0x000000010d15392d libdyld.dylib`start + 1
(lldb) 

Expected Results:
Should not crash. Isolated example also doesn’t crash.

Notes:
I can provide source code as well - big project though so first trying my luck with a compiled version.

Thanks!
Peter / @steipete


Update June 7, 2016: Fixed in Swift 2.2 (potentially even earlier)
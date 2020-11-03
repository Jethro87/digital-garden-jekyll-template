---
title: Implementing a multitasking blocking view in Flutter
---

My friend @bryandubno noticed that Clearful's latest release (featuring FaceID/TouchID) didn't implement a blocking view when multitasking (so the app's content can't be seen). After a bit of work, I managed to get it working in [Flutter](/flutter){: .internal-link}!

In `ios/Runner/AppDelegate.m`, add the following code:

```
- (void)applicationDidEnterBackground:(UIApplication *)application
{
    UIViewController *blankViewController = [UIViewController new];
    blankViewController.view.backgroundColor = [UIColor whiteColor];

    blankViewController.modalPresentationStyle = UIModalPresentationFullScreen;

    [self.window.rootViewController presentViewController:blankViewController animated:NO completion:NULL];
}

- (void)applicationWillEnterForeground:(UIApplication *)application
{
    [self.window.rootViewController dismissViewControllerAnimated:NO completion:NO];
}
```

If you're using Swift, instead add the following to `ios/Runner/AppDelegate.swift`:

```
    override func applicationWillResignActive(_ application: UIApplication){
    blankViewController = UIViewController()
    blankViewController!.view.backgroundColor = UIColor.white

    blankViewController!.modalPresentationStyle = .fullScreen
    self.window?.rootViewController?.present(blankViewController!, animated:false, completion:nil)
  }

  override func applicationDidBecomeActive(_ application: UIApplication) {
    if blankViewController != nil {
      self.window?.rootViewController?.dismiss(animated:false, completion:nil)
      blankViewController = nil
    }
  }
```

For Android, you'll want to change the contents of `android/app/src/main/kotlin/<your-app-name>/MainActivity.kt`:

```
    override func applicationWillResignActive(_ application: UIApplication){
    blankViewController = UIViewController()
    blankViewController!.view.backgroundColor = UIColor.white

    blankViewController!.modalPresentationStyle = .fullScreen
    self.window?.rootViewController?.present(blankViewController!, animated:false, completion:nil)
  }

  override func applicationDidBecomeActive(_ application: UIApplication) {
    if blankViewController != nil {
      self.window?.rootViewController?.dismiss(animated:false, completion:nil)
      blankViewController = nil
    }
  }
```

This [GitHub issue](https://github.com/flutter/flutter/issues/33236) was a great reference that allowed me to solve this issue.

# Localization for Mac App

This repository contains the code to add localization to your Mac app using Swift. Follow these steps to integrate it into your project.

## Step-by-Step Integration Guide

### Step 1: Add `LanguageBundel.swift` to Your Project

1. **Create a New Swift File**:
   - Open your Xcode project.
   - Right-click on the group where you want to add the new file (e.g., `Models`, `Utilities`).
   - Select `New File...`.
   - Choose `Swift File` and click `Next`.
   - Name the file `LanguageBundel.swift` and click `Create`.

2. **Add the Code**:
   - Copy and paste the following code into `LanguageBundel.swift`:
     ```swift
     import Foundation

     private var bundleKey: UInt8 = 0

     extension Bundle {
         class func setLanguage(_ language: String) {
             defer {
                 object_setClass(Bundle.main, BundleEx.self)
             }
             objc_setAssociatedObject(Bundle.main, &bundleKey, Bundle(path: Bundle.main.path(forResource: language, ofType: "lproj")!), .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
         }
     }

     private class BundleEx: Bundle {
         override func localizedString(forKey key: String, value: String?, table tableName: String?) -> String {
             if let bundle = objc_getAssociatedObject(self, &bundleKey) as? Bundle {
                 return bundle.localizedString(forKey: key, value: value, table: tableName)
             } else {
                 return super.localizedString(forKey: key, value: value, table: tableName)
             }
         }
     }
     ```

### Step 2: Add `LanguageSelectionView.swift` to Your Project

1. **Create a New Swift File**:
   - Right-click on the group where you want to add the new file.
   - Select `New File...`.
   - Choose `Swift File` and click `Next`.
   - Name the file `LanguageSelectionView.swift` and click `Create`.

2. **Add the Code**:
   - Copy and paste the following code into `LanguageSelectionView.swift`:
     ```swift
     import Foundation
     import SwiftUI

     class LanguageManager: ObservableObject {
         static let shared = LanguageManager()
         
         func setLanguage(languageCode: String) {
             UserDefaults.standard.set(languageCode, forKey: "appLanguage")
             Bundle.setLanguage(languageCode)
             NotificationCenter.default.post(name: NSNotification.Name("LanguageChanged"), object: nil)
             self.refreshView()
         }
         
         func getCurrentLanguage() -> String {
             return UserDefaults.standard.string(forKey: "appLanguage") ?? "en"
         }

         private func refreshView() {
             if let window = NSApplication.shared.windows.first {
                 window.contentView = NSHostingView(rootView: ContentView(viewModel: ChatViewModel()).environmentObject(LanguageManager.shared))
             }
         }
     }

     struct LanguageSelectionView: View {
         @Binding var selectedLanguage: String
         @Environment(\.colorScheme) var colorScheme

         var body: some View {
             HStack {
                 Picker("", selection: $selectedLanguage) {
                     Text("English").tag("en")
                         .foregroundColor(colorScheme == .dark ? .white : .init(hex: "000000"))
                     Text("Spanish").tag("es")
                         .foregroundColor(colorScheme == .dark ? .white : .init(hex: "000000"))
                 }
                 .pickerStyle(MenuPickerStyle())
                 .onChange(of: selectedLanguage) { newValue in
                     LanguageManager().setLanguage(languageCode: newValue)
                 }
             }
             .background(colorScheme == .dark ? Color.init(hex: "3C4146") : .init(hex: "E6E6EE"))
             .frame(width: 104, height: 40)
             .cornerRadius(8)
         }
     }
     ```

### Step 3: Create Localization Files

1. **Create `.lproj` Folders**:
   - In Xcode, create folders for each language you want to support. For example, `en.lproj` for English and `es.lproj` for Spanish.
   - Right-click on your project and select `New Group`. Name it `en.lproj`.
   - Repeat for each language you want to support.

2. **Add `Localizable.strings` Files**:
   - Inside each `.lproj` folder, create a new file.
   - Right-click on the folder, select `New File...`, choose `Strings File`, and name it `Localizable.strings`.
   - Add the localized strings for each language. For example:
     - `en.lproj/Localizable.strings`:
       ```
       "hello" = "Hello";
       ```
     - `es.lproj/Localizable.strings`:
       ```
       "hello" = "Hola";
       ```

### Step 4: Update Your Views

1. **Use Localized Strings**:
   - In your SwiftUI views or UIKit views, replace hardcoded strings with localized strings using `NSLocalizedString`.
   - For example:
     ```swift
     Text("key".localized)
     ```

2. **Add Language Selection View**:
   - Add the `LanguageSelectionView` to your main view to allow users to select the language.
   - For example, in your main view:
     ```swift
     struct ContentView: View {
         @State private var selectedLanguage = LanguageManager.shared.getCurrentLanguage()

         var body: some View {
             VStack {
                 LanguageSelectionView(selectedLanguage: $selectedLanguage)
             }
         }
     }
     ```

### Step 5: Test Your Localization

1. **Run Your App**:
   - Build and run your app in Xcode.
   - Use the language picker to switch between languages and ensure the localized strings are displayed correctly.

### Conclusion

By following these steps, you can integrate localization into your Mac app, providing a better user experience for a global audience. For more details and to access the code, visit my [GitHub Repository](https://github.com/Hafiz467/Mac-App-Localization).

Feel free to check it out, contribute, or provide feedback. Happy coding!

Best regard

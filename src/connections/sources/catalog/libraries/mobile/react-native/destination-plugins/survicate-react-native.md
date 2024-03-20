---
title: React Native Survicate Plugin
strat: react-native
---

[Survicate](https://survicate.com/){:target="_blank"} is an all-in-one customer feedback platform that helps you collect and act on feedback from your customers. It helps you understand your customers and improve their experience with your product or service.


## Installation

You need to install the `@survicate/analytics-react-native-survicate` and the `@survicate/react-native-survicate` dependencies.

Using NPM:
```bash
npm install --save @survicate/analytics-react-native-survicate @survicate/react-native-survicate
```

Using Yarn:
```bash
yarn add @survicate/analytics-react-native-survicate @survicate/react-native-survicate
```

### Configuring
1. Add your Survicate workspace key to `Info.plist`
```
	<key>Survicate</key>
	<dict>
		<key>WorkspaceKey</key>
		<string>YOUR_WORKSPACE_KEY</string>
	</dict>
```
2. run command `pod install` in your `ios` directory

### Configuring Survicate Bindings for Android

1. Add maven repository to your project `build.gradle` located under `android` directory
```
allprojects {
    repositories {
        // ...
        maven { url 'https://repo.survicate.com' }
    }
}
```

2. Add your Survicate workspace key to `AndroidManifest.xml`

```java
<application
    android:name=".MyApp"
>
    <!-- ... -->
    <meta-data android:name="com.survicate.surveys.workspaceKey" android:value="YOUR_WORKSPACE_KEY"/>
</application>
```

## Usage

Follow the [instructions for adding plugins](https://github.com/segmentio/analytics-react-native#adding-plugins){:target="_blank"} on the main Analytics client:

In your code where you initialize the analytics client call the `.add(plugin)` method with an `SurvicatePlugin` instance:

```ts
import { createClient } from '@segment/analytics-react-native';

import { SurvicatePlugin } from '@segment/analytics-react-native-plugin-survicate';

const segmentClient = createClient({
  writeKey: 'SEGMENT_KEY'
});

segmentClient.add({ plugin: new SurvicatePlugin() });
```

### Using the Survicate plugin

#### Identify

In the SurvicateDestination plugin, the Identify event from Segment is transferred to the setUserTrait method of Survicate. This is achieved within the identify function of the SurvicateDestination class. The traits and userId from the Identify event are extracted and set as user traits in Survicate using the setUserTrait method. The traits are a dictionary where each key-value pair is set as a user trait. The userId is also set as a user trait with the key "userId".

#### Track

In the SurvicateDestination plugin, the Track method from Segment is used as the invokeEvent method in Survicate. This means that when you track an event in Segment, it will be invoked in Survicate.

#### Screen

Similarly, the Screen method from Segment is used as the enterScreen method in Survicate. This means that when you track a screen in Segment, it will be entered in Survicate.

#### Reset

The reset method from Segment is used as the reset method in Survicate. This means that when you reset the Segment client, the Survicate client will also be reset.
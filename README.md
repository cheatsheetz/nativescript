# NativeScript Cheat Sheet

## Quick Reference

NativeScript enables building native mobile applications using JavaScript, TypeScript, Angular, Vue.js, or React, providing direct access to native APIs.

### Setup and Installation

```bash
# Install NativeScript CLI
npm install -g @nativescript/cli

# Verify installation
ns doctor

# Create new project
ns create myApp --js                # JavaScript
ns create myApp --ts                # TypeScript
ns create myApp --ng                # Angular
ns create myApp --vue               # Vue.js
ns create myApp --react             # React

# Add platforms
ns platform add android
ns platform add ios

# Run app
ns run android
ns run ios
ns run android --device
ns run ios --device

# Debug
ns debug android
ns debug ios
```

## Project Structure

```
myApp/
├── App_Resources/          # Platform resources
│   ├── Android/           # Android resources
│   └── iOS/              # iOS resources
├── src/                   # Source code
│   ├── app/              # Application code
│   ├── assets/           # Static assets
│   └── main.ts           # Entry point
├── platforms/            # Platform-specific code
├── node_modules/         # Dependencies
├── package.json          # Project configuration
└── nativescript.config.ts # NS configuration
```

## Core JavaScript/TypeScript

### Basic App Structure

```typescript
// main.ts
import { Application } from '@nativescript/core';

Application.run({ moduleName: 'app-root' });
```

```xml
<!-- app-root.xml -->
<Page xmlns="http://schemas.nativescript.org/tns.xsd" loaded="pageLoaded">
  <ActionBar title="My App" />
  <StackLayout>
    <Label text="Welcome to NativeScript!" class="h1 text-center" />
    <Button text="Click me!" tap="onButtonTap" class="btn btn-primary" />
  </StackLayout>
</Page>
```

```typescript
// app-root.ts
import { EventData, Page } from '@nativescript/core';

export function pageLoaded(args: EventData) {
    const page = <Page>args.object;
    console.log('Page loaded');
}

export function onButtonTap() {
    console.log('Button tapped!');
}
```

### Data Binding

```xml
<!-- Two-way data binding -->
<Page>
    <StackLayout>
        <TextField text="{{ username }}" hint="Enter username" />
        <Label text="{{ 'Hello, ' + username + '!' }}" />
        <Button text="Submit" tap="{{ onSubmit }}" isEnabled="{{ isValid }}" />
    </StackLayout>
</Page>
```

```typescript
import { Observable } from '@nativescript/core';

export class MainViewModel extends Observable {
    private _username: string = '';
    
    get username(): string {
        return this._username;
    }
    
    set username(value: string) {
        if (this._username !== value) {
            this._username = value;
            this.notifyPropertyChange('username', value);
            this.notifyPropertyChange('isValid', this.isValid);
        }
    }
    
    get isValid(): boolean {
        return this.username.length > 0;
    }
    
    onSubmit() {
        if (this.isValid) {
            console.log('Submitted:', this.username);
        }
    }
}
```

### UI Components

```xml
<!-- Layout Components -->
<StackLayout orientation="vertical" padding="20">
    <Label text="Stack Layout - Vertical" />
    <Button text="Button 1" />
    <Button text="Button 2" />
</StackLayout>

<GridLayout rows="auto, *, auto" columns="*, 2*">
    <Label text="Header" row="0" colSpan="2" />
    <Label text="Sidebar" row="1" col="0" />
    <Label text="Content" row="1" col="1" />
    <Label text="Footer" row="2" colSpan="2" />
</GridLayout>

<FlexboxLayout flexDirection="row" justifyContent="space-around">
    <Label text="Item 1" />
    <Label text="Item 2" />
    <Label text="Item 3" />
</FlexboxLayout>

<!-- Input Components -->
<TextField hint="Enter text" text="{{ inputText }}" />
<TextView hint="Enter long text" text="{{ longText }}" />
<Switch checked="{{ isToggled }}" />
<Slider value="{{ sliderValue }}" minValue="0" maxValue="100" />
<DatePicker date="{{ selectedDate }}" />
<TimePicker time="{{ selectedTime }}" />

<!-- List Components -->
<ListView items="{{ items }}" itemTap="{{ onItemTap }}">
    <ListView.itemTemplate>
        <Label text="{{ name }}" />
    </ListView.itemTemplate>
</ListView>

<RadListView items="{{ items }}">
    <RadListView.itemTemplate>
        <StackLayout>
            <Label text="{{ title }}" class="h3" />
            <Label text="{{ description }}" />
        </StackLayout>
    </RadListView.itemTemplate>
</RadListView>
```

## Angular Integration

### Angular Setup

```bash
# Create Angular project
ns create myAngularApp --ng

# Add Angular dependencies
npm install @angular/core @angular/common @angular/forms
```

```typescript
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'ns-app',
  templateUrl: './app.component.html'
})
export class AppComponent {
  title = 'My NativeScript App';
  items = [
    { id: 1, name: 'Item 1' },
    { id: 2, name: 'Item 2' },
    { id: 3, name: 'Item 3' }
  ];
  
  onItemTap(event: any) {
    console.log('Item tapped:', event.index);
  }
}
```

```html
<!-- app.component.html -->
<ActionBar [title]="title"></ActionBar>
<StackLayout>
  <ListView [items]="items" (itemTap)="onItemTap($event)">
    <ng-template let-item="item">
      <Label [text]="item.name"></Label>
    </ng-template>
  </ListView>
</StackLayout>
```

### Angular Services

```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { Http } from '@nativescript/core';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  
  async getUsers(): Promise<any[]> {
    try {
      const response = await Http.getJSON('https://jsonplaceholder.typicode.com/users');
      return response as any[];
    } catch (error) {
      console.error('Error fetching users:', error);
      return [];
    }
  }
  
  async postData(data: any): Promise<any> {
    try {
      const response = await Http.request({
        url: 'https://api.example.com/data',
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        content: JSON.stringify(data)
      });
      
      return response.content.toJSON();
    } catch (error) {
      console.error('Error posting data:', error);
      throw error;
    }
  }
}
```

### Angular Routing

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { NativeScriptRouterModule } from '@nativescript/angular';
import { Routes } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';

const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserDetailComponent }
];

@NgModule({
  imports: [NativeScriptRouterModule.forRoot(routes)],
  exports: [NativeScriptRouterModule]
})
export class AppRoutingModule { }
```

```typescript
// Navigation in component
import { Router, ActivatedRoute } from '@angular/router';
import { RouterExtensions } from '@nativescript/angular';

export class HomeComponent {
  constructor(
    private router: Router,
    private routerExtensions: RouterExtensions,
    private route: ActivatedRoute
  ) {}
  
  navigateToAbout() {
    this.routerExtensions.navigate(['/about']);
  }
  
  navigateWithParams() {
    this.routerExtensions.navigate(['/user', 123], {
      queryParams: { name: 'John' }
    });
  }
  
  goBack() {
    this.routerExtensions.back();
  }
}
```

## Vue.js Integration

### Vue Setup

```bash
# Create Vue project
ns create myVueApp --vue
```

```vue
<!-- Home.vue -->
<template>
  <Page>
    <ActionBar title="Home" />
    <StackLayout>
      <Label :text="message" class="h1 text-center" />
      <TextField v-model="inputText" hint="Enter text" />
      <Button text="Add Item" @tap="addItem" />
      
      <ListView for="item in items" @itemTap="onItemTap">
        <v-template>
          <Label :text="item.name" />
        </v-template>
      </ListView>
    </StackLayout>
  </Page>
</template>

<script>
export default {
  data() {
    return {
      message: 'Welcome to Vue + NativeScript!',
      inputText: '',
      items: [
        { id: 1, name: 'Item 1' },
        { id: 2, name: 'Item 2' }
      ]
    };
  },
  methods: {
    addItem() {
      if (this.inputText.trim()) {
        this.items.push({
          id: Date.now(),
          name: this.inputText
        });
        this.inputText = '';
      }
    },
    onItemTap(event) {
      console.log('Item tapped:', event.index);
    }
  }
};
</script>

<style scoped>
.h1 {
  font-size: 24;
  margin: 20;
}
</style>
```

### Vue Navigation

```javascript
// main.js
import Vue from 'nativescript-vue';
import Home from './components/Home.vue';
import About from './components/About.vue';

// Register components
Vue.component('Home', Home);
Vue.component('About', About);

new Vue({
  render: h => h('frame', [h(Home)])
}).$start();
```

```vue
<!-- Navigation in component -->
<template>
  <Page>
    <ActionBar title="Home">
      <ActionItem text="About" @tap="navigateToAbout" />
    </ActionBar>
    <StackLayout>
      <Button text="Go to About" @tap="navigateToAbout" />
    </StackLayout>
  </Page>
</template>

<script>
import About from './About.vue';

export default {
  methods: {
    navigateToAbout() {
      this.$navigateTo(About, {
        props: { message: 'Hello from Home!' }
      });
    }
  }
};
</script>
```

## Native API Access

### Device Information

```typescript
import { Device, Screen } from '@nativescript/core';

export class DeviceInfo {
  getDeviceDetails() {
    return {
      model: Device.model,
      deviceType: Device.deviceType,
      os: Device.os,
      osVersion: Device.osVersion,
      sdkVersion: Device.sdkVersion,
      language: Device.language,
      manufacturer: Device.manufacturer,
      uuid: Device.uuid
    };
  }
  
  getScreenInfo() {
    return {
      widthDIPs: Screen.mainScreen.widthDIPs,
      heightDIPs: Screen.mainScreen.heightDIPs,
      widthPixels: Screen.mainScreen.widthPixels,
      heightPixels: Screen.mainScreen.heightPixels,
      scale: Screen.mainScreen.scale
    };
  }
}
```

### File System

```typescript
import { Folder, File, path, knownFolders } from '@nativescript/core';

export class FileManager {
  
  async writeFile(filename: string, content: string): Promise<void> {
    const documents = knownFolders.documents();
    const file = documents.getFile(filename);
    
    await file.writeText(content);
  }
  
  async readFile(filename: string): Promise<string> {
    const documents = knownFolders.documents();
    const file = documents.getFile(filename);
    
    if (await file.exists()) {
      return await file.readText();
    }
    return '';
  }
  
  async deleteFile(filename: string): Promise<void> {
    const documents = knownFolders.documents();
    const file = documents.getFile(filename);
    
    if (await file.exists()) {
      await file.remove();
    }
  }
  
  async createFolder(folderName: string): Promise<Folder> {
    const documents = knownFolders.documents();
    return documents.getFolder(folderName);
  }
  
  async listFiles(): Promise<string[]> {
    const documents = knownFolders.documents();
    const entities = await documents.getEntities();
    
    return entities
      .filter(entity => !entity.isFolder)
      .map(entity => entity.name);
  }
}
```

### Camera and Gallery

```typescript
import { ImageSource, ImageAsset } from '@nativescript/core';
import * as camera from '@nativescript/camera';

export class CameraService {
  
  async requestPermissions(): Promise<boolean> {
    return await camera.requestPermissions();
  }
  
  async takePicture(): Promise<ImageSource> {
    const options = {
      width: 300,
      height: 300,
      keepAspectRatio: false,
      saveToGallery: true
    };
    
    const imageAsset = await camera.takePicture(options);
    return await ImageSource.fromAsset(imageAsset);
  }
  
  async selectFromGallery(): Promise<ImageSource> {
    const options = {
      width: 300,
      height: 300,
      keepAspectRatio: false
    };
    
    const imageAsset = await camera.requestPermissions()
      .then(() => {
        return imagepicker.create({
          mode: 'single'
        }).authorize();
      })
      .then(() => {
        return imagepicker.create({
          mode: 'single'
        }).present();
      });
      
    if (imageAsset.length > 0) {
      return await ImageSource.fromAsset(imageAsset[0]);
    }
    
    throw new Error('No image selected');
  }
}
```

### Location Services

```typescript
import { Geolocation, Location } from '@nativescript/geolocation';
import { CoreTypes } from '@nativescript/core';

export class LocationService {
  
  async enableLocationRequest(): Promise<boolean> {
    return await Geolocation.enableLocationRequest();
  }
  
  async getCurrentLocation(): Promise<Location> {
    const location = await Geolocation.getCurrentLocation({
      desiredAccuracy: CoreTypes.Accuracy.high,
      maximumAge: 5000,
      timeout: 20000
    });
    
    return location;
  }
  
  watchLocation(callback: (location: Location) => void): number {
    return Geolocation.watchLocation(
      callback,
      (error) => {
        console.error('Location error:', error);
      },
      {
        desiredAccuracy: CoreTypes.Accuracy.high,
        updateDistance: 1.0,
        minimumUpdateTime: 1000
      }
    );
  }
  
  clearWatch(watchId: number): void {
    Geolocation.clearWatch(watchId);
  }
  
  calculateDistance(lat1: number, lon1: number, lat2: number, lon2: number): number {
    const R = 6371; // Radius of the Earth in kilometers
    const dLat = this.deg2rad(lat2 - lat1);
    const dLon = this.deg2rad(lon2 - lon1);
    
    const a = 
      Math.sin(dLat/2) * Math.sin(dLat/2) +
      Math.cos(this.deg2rad(lat1)) * Math.cos(this.deg2rad(lat2)) * 
      Math.sin(dLon/2) * Math.sin(dLon/2);
    
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    const distance = R * c; // Distance in kilometers
    
    return distance;
  }
  
  private deg2rad(deg: number): number {
    return deg * (Math.PI / 180);
  }
}
```

## Platform-specific Code

### Android-specific

```typescript
// android-utils.ts
import { Application, Utils } from '@nativescript/core';

export class AndroidUtils {
  
  static showToast(message: string): void {
    if (Application.android) {
      const Toast = android.widget.Toast;
      Toast.makeText(
        Utils.android.getApplicationContext(), 
        message, 
        Toast.LENGTH_LONG
      ).show();
    }
  }
  
  static vibrate(duration: number = 500): void {
    if (Application.android) {
      const context = Utils.android.getApplicationContext();
      const vibrator = context.getSystemService(android.content.Context.VIBRATOR_SERVICE);
      
      if (android.os.Build.VERSION.SDK_INT >= 26) {
        const vibrationEffect = android.os.VibrationEffect.createOneShot(
          duration, 
          android.os.VibrationEffect.DEFAULT_AMPLITUDE
        );
        vibrator.vibrate(vibrationEffect);
      } else {
        vibrator.vibrate(duration);
      }
    }
  }
  
  static checkPermission(permission: string): boolean {
    if (Application.android) {
      const context = Utils.android.getApplicationContext();
      return android.support.v4.content.ContextCompat.checkSelfPermission(
        context, 
        permission
      ) === android.content.pm.PackageManager.PERMISSION_GRANTED;
    }
    return false;
  }
}
```

### iOS-specific

```typescript
// ios-utils.ts
import { Application, Device } from '@nativescript/core';

export class IOSUtils {
  
  static showAlert(title: string, message: string): void {
    if (Application.ios) {
      const alertController = UIAlertController.alertControllerWithTitleMessagePreferredStyle(
        title,
        message,
        UIAlertControllerStyle.Alert
      );
      
      const okAction = UIAlertAction.actionWithTitleStyleHandler(
        'OK',
        UIAlertActionStyle.Default,
        null
      );
      
      alertController.addAction(okAction);
      
      const rootViewController = Application.ios.rootController;
      rootViewController.presentViewControllerAnimatedCompletion(
        alertController,
        true,
        null
      );
    }
  }
  
  static vibrate(): void {
    if (Application.ios) {
      const AudioServicesPlaySystemSound = interop.declare(
        AudioServicesPlaySystemSound,
        interop.types.void,
        [interop.types.uint32]
      );
      AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
    }
  }
  
  static setStatusBarStyle(style: 'light' | 'dark'): void {
    if (Application.ios) {
      const statusBarStyle = style === 'light' 
        ? UIStatusBarStyle.LightContent 
        : UIStatusBarStyle.Default;
        
      UIApplication.sharedApplication.statusBarStyle = statusBarStyle;
    }
  }
}
```

## Performance Optimization

### Bundle Optimization

```typescript
// main.ts - Code splitting
import { Application } from '@nativescript/core';

// Lazy load heavy modules
async function loadHeavyModule() {
  const module = await import('./heavy-module');
  return module;
}

Application.run({ moduleName: 'app-root' });
```

### Memory Management

```typescript
// memory-manager.ts
import { Application, GC } from '@nativescript/core';

export class MemoryManager {
  
  static forceGarbageCollection(): void {
    if (global.gc) {
      global.gc();
    }
    
    // Platform-specific GC
    if (Application.android) {
      java.lang.System.gc();
    } else if (Application.ios) {
      // iOS automatically manages memory
    }
  }
  
  static getMemoryUsage(): any {
    if (Application.android) {
      const runtime = java.lang.Runtime.getRuntime();
      return {
        totalMemory: runtime.totalMemory(),
        freeMemory: runtime.freeMemory(),
        maxMemory: runtime.maxMemory(),
        usedMemory: runtime.totalMemory() - runtime.freeMemory()
      };
    }
    
    return null;
  }
  
  static optimizeImages(): void {
    // Implement image optimization logic
    // - Resize large images
    // - Use appropriate formats
    // - Implement lazy loading
  }
}
```

## Testing

### Unit Testing

```typescript
// user.service.spec.ts
import { TestBed } from '@angular/core/testing';
import { UserService } from './user.service';

describe('UserService', () => {
  let service: UserService;
  
  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(UserService);
  });
  
  it('should be created', () => {
    expect(service).toBeTruthy();
  });
  
  it('should fetch users', async () => {
    const users = await service.getUsers();
    expect(users).toBeDefined();
    expect(Array.isArray(users)).toBeTruthy();
  });
});
```

### E2E Testing

```typescript
// e2e/app.e2e-spec.ts
import { AppiumDriver, createDriver } from 'nativescript-dev-appium';

describe('App E2E Tests', () => {
  let driver: AppiumDriver;
  
  beforeAll(async () => {
    driver = await createDriver();
  });
  
  afterAll(async () => {
    await driver.quit();
  });
  
  it('should display welcome message', async () => {
    const welcomeText = await driver.findElementByText('Welcome');
    expect(await welcomeText.isDisplayed()).toBe(true);
  });
  
  it('should navigate to second page', async () => {
    const button = await driver.findElementByText('Go to Second Page');
    await button.click();
    
    const secondPageText = await driver.findElementByText('Second Page');
    expect(await secondPageText.isDisplayed()).toBe(true);
  });
});
```

## Build and Deployment

### Build Configuration

```typescript
// nativescript.config.ts
import { NativeScriptConfig } from '@nativescript/cli';

export default {
  id: 'com.example.myapp',
  appPath: 'src',
  appResourcesPath: 'App_Resources',
  android: {
    v8Flags: '--expose_gc',
    markingMode: 'none'
  },
  ios: {
    discardUncaughtJsExceptions: true
  }
} as NativeScriptConfig;
```

### Release Builds

```bash
# Android release build
ns build android --release --key-store-path myapp.keystore --key-store-password password --key-store-alias myapp --key-store-alias-password password

# iOS release build
ns build ios --for-device --release

# Clean build
ns clean
ns build android --release
```

### Code Signing

```bash
# Generate Android keystore
keytool -genkey -v -keystore myapp.keystore -alias myapp -keyalg RSA -keysize 2048 -validity 10000

# iOS code signing (via Xcode)
# 1. Open project in Xcode
# 2. Configure signing & capabilities
# 3. Archive and distribute
```

## Popular Plugins

### Essential Plugins

```bash
# HTTP requests
ns plugin add @nativescript/core

# Local storage
ns plugin add @nativescript/localstorage

# Social sharing
ns plugin add @nativescript/social-share

# Device orientation
ns plugin add @nativescript/orientation

# Fingerprint authentication
ns plugin add @nativescript/fingerprint-auth

# Push notifications
ns plugin add @nativescript/push-notifications

# Image picker
ns plugin add @nativescript/imagepicker

# PDF generation
ns plugin add @nativescript/pdf

# QR/Barcode scanner
ns plugin add @nativescript/barcodescanner

# Maps
ns plugin add @nativescript/google-maps
```

### Plugin Usage Example

```typescript
// Using social sharing plugin
import { SocialShare } from '@nativescript/social-share';

export class ShareService {
  
  shareText(text: string): void {
    SocialShare.shareText(text, 'Check this out!');
  }
  
  shareImage(imagePath: string): void {
    SocialShare.shareImage(imagePath, 'Amazing photo!');
  }
  
  shareUrl(url: string): void {
    SocialShare.shareUrl(url, 'Interesting article', 'Check out this link');
  }
}
```

## Official Resources

- [NativeScript Documentation](https://docs.nativescript.org/)
- [NativeScript Playground](https://play.nativescript.org/)
- [NativeScript Plugins](https://market.nativescript.org/)
- [NativeScript CLI Reference](https://docs.nativescript.org/tooling/cli)
- [NativeScript Blog](https://blog.nativescript.org/)

## Community and Tools

- [NativeScript GitHub](https://github.com/NativeScript/NativeScript)
- [NativeScript Community Forum](https://discourse.nativescript.org/)
- [NativeScript Discord](https://discord.com/invite/RgmpGky9GR)
- [Awesome NativeScript](https://github.com/bradmartin/awesome-nativescript)
- [NativeScript Sidekick](https://www.nativescript.org/nativescript-sidekick)
- [NativeScript YouTube](https://www.youtube.com/c/NativeScript)
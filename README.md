# TryOutfit App - Documentation

## Overview

**TryOutfit App** is an AI-powered virtual try-on mobile application that allows users to visualize how clothing items would look on them. Users upload their photo, select a clothing image, and receive an AI-generated result showing them wearing the outfit.

## Table of Contents

- [Technology Stack](#technology-stack)
- [Features](#features)
- [Application Flow](#application-flow)
- [Architecture](#architecture)
- [State Management](#state-management)
- [Key Dependencies](#key-dependencies)
- [Screens](#screens)
- [API Integration](#api-integration)
- [Permissions](#permissions)
- [Platform Support](#platform-support)

## Technology Stack

### Core Framework
- **React Native**: `0.81.5` - Cross-platform mobile development
- **Expo**: `~54.0.25` - Development platform and tooling
- **TypeScript**: `~5.9.2` - Type-safe JavaScript
- **React**: `19.1.0` - UI library

### Navigation
- **Expo Router**: `~6.0.15` - File-based routing system

### State Management
- **@tanstack/react-query**: `^5.83.0` - Server state management and data fetching
- **@nkzw/create-context-hook**: `^1.1.0` - Context-based local state management

### UI/UX Libraries
- **expo-linear-gradient**: `~15.0.7` - Gradient backgrounds
- **expo-glass-effect**: `~0.1.7` - iOS glassmorphism effects
- **expo-blur**: `~15.0.7` - Blur effects for Android
- **lucide-react-native**: `^0.475.0` - Icon library
- **react-native-safe-area-context**: `~5.6.0` - Safe area handling

### Media & File Handling
- **expo-image-picker**: `~17.0.8` - Camera and gallery access
- **expo-image**: `~3.0.10` - Optimized image component
- **expo-media-library**: `~18.2.0` - Save images to device
- **expo-file-system**: `~19.0.19` - File operations
- **expo-sharing**: `~14.0.7` - Share functionality

### AI Integration
- **@rork-ai/toolkit-sdk**: AI image generation and editing

### Other Expo Modules
- **expo-haptics**: `~15.0.7` - Haptic feedback
- **expo-constants**: `~18.0.10` - App constants
- **expo-status-bar**: `~3.0.8` - Status bar control

## Features

### 1. **AI-Powered Virtual Try-On**
- Upload a photo of yourself
- Select clothing from your gallery or camera
- Generate realistic images showing you wearing the outfit
- Powered by advanced AI image editing technology

### 2. **Image Management**
- Take photos using device camera
- Select images from photo library
- Image cropping with 3:4 aspect ratio
- Base64 encoding for API transmission

### 3. **Result Actions**
- **Save**: Download generated images to device gallery
- **Share**: Share results with other apps
- **Try Again**: Test different clothing options
- **Start Over**: Begin a new session

### 4. **Beautiful UI/UX**
- Modern glassmorphism design
- Platform-specific glass effects (iOS native, Android blur)
- Gradient backgrounds and buttons
- Smooth animations and haptic feedback
- Step-by-step guided process

### 5. **Cross-Platform**
- iOS support
- Android support
- Web compatibility (React Native Web)

## Application Flow

### Step 1: Upload Your Photo
1. User arrives at welcome screen
2. Taps "Get Started"
3. Chooses to take a photo or select from gallery
4. Photo is cropped to 3:4 aspect ratio
5. Proceeds to clothing selection

### Step 2: Choose Clothing
1. User selects clothing image
2. Can take a photo or choose from gallery
3. Clothing is cropped to 3:4 aspect ratio
4. Taps "Try It On" to generate result

### Step 3: View Result
1. AI automatically generates try-on image
2. User sees loading state during generation
3. Result is displayed once complete
4. User can save, share, try different clothing, or start over

## Architecture

### File Structure
```
app/
├── _layout.tsx          # Root layout with QueryClient provider
├── index.tsx            # Welcome screen
├── step1.tsx            # User photo upload
├── step2.tsx            # Clothing selection
└── step3.tsx            # Result generation and display

contexts/
└── TryOutfitContext.tsx # Global state for photos

constants/
└── colors.ts            # Color constants

assets/
└── images/              # App icons and assets
```

### Routing Architecture
- **Stack-based navigation** using Expo Router
- No tab navigation (full-screen experience)
- Simple linear flow: Welcome → Step1 → Step2 → Step3
- Routes:
  - `/` - Welcome screen
  - `/step1` - Upload photo
  - `/step2` - Choose clothing
  - `/step3` - View result

## State Management

### Global State (TryOutfitContext)
Manages the application's core data using `@nkzw/create-context-hook`:

```typescript
interface PhotoData {
  uri: string;
  base64?: string;
}

// State managed:
- userPhoto: PhotoData | null
- clothingPhoto: PhotoData | null
- resultPhoto: PhotoData | null

// Actions:
- setUserPhoto()
- setClothingPhoto()
- setResultPhoto()
- reset() - Clear all photos
- resetClothing() - Clear clothing and result only
```

### Server State
- **React Query** handles API mutations for image generation
- Automatic loading and error states
- Retry logic for failed requests

## Key Dependencies

### Image Picker Configuration
```json
{
  "mediaTypes": ["images"],
  "allowsEditing": true,
  "aspect": [3, 4],
  "quality": 0.8,
  "base64": true
}
```

### Platform-Specific Glass Effect
- **iOS**: Uses `expo-glass-effect` with native glassmorphism
- **Android/Web**: Falls back to `expo-blur` with blur effects

## Screens

### 1. Welcome Screen (`index.tsx`)
- **Route**: `/`
- **Purpose**: Introduction and app overview
- **Features**:
  - Hero section with app logo
  - Three-step process overview
  - "Get Started" CTA button
  - Background image with gradient overlay

### 2. Step 1 - Upload Photo (`step1.tsx`)
- **Route**: `/step1`
- **Purpose**: User photo capture/selection
- **Features**:
  - Camera capture
  - Gallery selection
  - Image preview with remove option
  - Progress indicator (Step 1 of 3)
  - Navigation to next step

### 3. Step 2 - Choose Clothing (`step2.tsx`)
- **Route**: `/step2`
- **Purpose**: Clothing image selection
- **Features**:
  - Camera capture
  - Gallery selection
  - Image preview with remove option
  - Back button
  - "Try It On" button
  - Progress indicator (Step 2 of 3)

### 4. Step 3 - Result (`step3.tsx`)
- **Route**: `/step3`
- **Purpose**: AI generation and result display
- **Features**:
  - Automatic AI generation on mount
  - Loading state with spinner
  - Result preview
  - Save to gallery
  - Share functionality
  - Try different clothing option
  - Start over button
  - Progress indicator (Step 3 of 3)

## API Integration

### Rork Toolkit Image Edit API
**Endpoint**: `https://toolkit.rork.com/images/edit/`

**Request**:
```json
{
  "prompt": "Show this person wearing the clothing from the second image...",
  "images": [
    { "type": "image", "image": "base64_user_photo" },
    { "type": "image", "image": "base64_clothing_photo" }
  ],
  "aspectRatio": "3:4"
}
```

**Response**:
```json
{
  "image": {
    "mimeType": "image/jpeg",
    "base64Data": "base64_result_image"
  }
}
```

## Permissions

### iOS (Info.plist)
- **NSPhotoLibraryUsageDescription**: Access photos for try-on
- **NSCameraUsageDescription**: Take photos for try-on
- **NSPhotoLibraryAddUsageDescription**: Save generated results

### Android
- `CAMERA` - Camera access
- `READ_EXTERNAL_STORAGE` - Read photos
- `WRITE_EXTERNAL_STORAGE` - Save photos
- `READ_MEDIA_IMAGES` - Read media images
- `INTERNET` - API calls

## Platform Support

### iOS
- Native glass effects using `expo-glass-effect`
- Full camera and media library support
- Native sharing functionality

### Android
- Blur effects using `expo-blur`
- Full camera and media library support
- Native sharing functionality

### Web
- Limited camera support (uses browser APIs)
- File input for image selection
- Download instead of save to gallery
- Web Share API for sharing

## Development

### Scripts
```bash
# Start development server with tunnel
npm start

# Start web version
npm run start-web

# Start web with debug logs
npm run start-web-dev

# Run linter
npm run lint
```

## Design System

### Color Scheme
- Primary gradient: Purple (`#8B5CF6`) to Indigo (`#6366F1`)
- Background: Dark overlays with gradient
- Text: White with varying opacity
- Glass effects: Semi-transparent white borders

### Typography
- Title: 48px, bold (Welcome screen)
- Section titles: 32px, bold
- Body text: 16px, regular
- Buttons: 18px, bold

### Spacing
- Container padding: 24px
- Element gaps: 12px
- Safe area insets: Respected on all screens

## Future Enhancement Opportunities

1. **User Accounts**: Save try-on history
2. **Clothing Catalog**: Pre-loaded clothing options
3. **Body Measurements**: Size recommendations
4. **Fashion Recommendations**: AI-powered styling suggestions
5. **Wardrobe Management**: Save favorite outfits
6. **Social Features**: Share to social media directly
7. **Multiple Outfits**: Try multiple clothing items at once
8. **AR Preview**: Real-time augmented reality try-on

## Troubleshooting

### Common Issues

**Image not generating**
- Check internet connection
- Verify API endpoint is accessible
- Ensure images are properly encoded in base64

**Permission errors**
- Check device settings for camera/photo permissions
- Re-request permissions if denied

**Sharing not working on web**
- Web Share API may not be supported
- Falls back to download on unsupported browsers

## License

Personnal project - All rights reserved


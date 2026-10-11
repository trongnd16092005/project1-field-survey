# Mini-Project 1: VKU Field Survey

## Project Information

- **Course:** Mini-Project 1
- **Project:** VKU Field Survey — Offline Field Data Collection (PWA and Capacitor iOS)
- **Student:** Đức Trọng
- **Email:** trongnd.23itb@vku.udn.vn
- **Student ID / Team:** Not provided in the supplied project files
- **Report date:** October 11, 2026

## Deliverable Links

- **Source code:** Local project folder supplied for this report: `VKU-Field-Survy-main/VKU-Field-Survy-main`
- **GitHub repository:** Not supplied or configured in the local project folder
- **Demo:** The README contains a legacy GitHub Pages URL. It was not verified for this report, so it should be checked before submission.
- **Demo video:** Not provided

## 1. Project Overview

VKU Field Survey is a mobile-first application for recording campus facility and equipment conditions. It is implemented as a TypeScript web application with Progressive Web App (PWA) support and Capacitor integration for native iOS device features. The app is designed for field use where connectivity may be unavailable: a survey can be saved on the device, placed in a synchronization queue, and processed when a connection returns.

The local source shows a Vite and TypeScript project rather than a React Native or Expo application. Capacitor provides the native bridge for camera and location on iOS, while browser APIs and fallbacks support web use. The current synchronization destination is a mock server implemented in the application; this is suitable for demonstrating the queue flow, but it is not a connection to a VKU production backend.

## 2. Required Features and Implementation Status

The statuses below are based on inspection of the local source code. They describe implementation evidence and are not a substitute for runtime acceptance testing.

| Feature | Evidence in source | Status |
|---|---|---|
| Mobile-oriented survey workflow | Survey form, responsive application shell, and navigation between survey, list, map, and synchronization views | Implemented in source; runtime not verified |
| Facility survey fields and validation | Form validates building, floor/room, category, equipment, inspector name, and email | Implemented in source |
| Draft persistence | Form saves a draft after a short debounce; IndexedDB contains a drafts store | Implemented in source |
| Offline survey submission | Submission writes the survey locally and adds a CREATE operation to the sync queue before attempting network synchronization | Implemented in source |
| Offline-first storage | IndexedDB database `VKU_FIELD_SURVEY_DB` contains drafts, surveys, syncQueue, and settings stores | Implemented in source |
| Queue processing and retry | Queue service processes pending/failed operations in FIFO order, tracks status and retry information, and supports retry/manual synchronization | Implemented in source; destination is a mock server |
| Connection state handling | Service listens to browser online/offline events and Capacitor Network status; it also checks periodically while online | Implemented in source |
| PWA caching and offline navigation | Service worker caches the app shell/runtime resources and provides a navigation fallback; Background Sync messages clients to process the queue | Implemented in source; operating-system background execution is platform-dependent |
| Camera capture | Capacitor Camera integration with web fallback; image is resized/compressed on the client | Implemented in source; device behavior not verified |
| GPS and campus buildings | Location service uses device location and campus building coordinates to select a nearby building | Implemented in source; if location is denied or unavailable, a campus-center fallback is used and may be approximate |
| Survey list, filters, and export | Survey list component provides browsing tools and JSON export | Implemented in source |
| Campus map and severity visualization | Campus data and map view display building locations and survey severity/status markers | Implemented in source; visual behavior not verified |

## 3. Architecture and Project Structure

The application is organized by responsibility:

- `src/main.ts` initializes the application, composes the views, registers the service worker, and seeds demonstration records.
- `src/components/SurveyForm.ts` handles survey entry, validation, draft autosave, photo/location inputs, and submission.
- `src/components/SurveyList.ts` presents saved surveys and list actions such as filtering and export.
- `src/components/SyncModal.ts` exposes synchronization queue status and retry controls.
- `src/data/campusData.ts` stores campus building coordinates and survey categories.
- `src/types/survey.ts` defines survey, facility status, and synchronization status types.
- `src/db/indexedDB.ts` creates and accesses the local IndexedDB database and its stores.
- `src/services/syncService.ts` saves local changes, manages the queue, and coordinates retries and connection changes.
- `src/services/mockServer.ts` simulates network requests and stores its mock server data in browser local storage.
- `src/services/hardwareService.ts` provides camera and geolocation access through Capacitor with web fallbacks.
- `public/sw.js` implements service-worker caching, offline navigation fallback, and a message path for queue processing.

The principal data path is local-first: the form validates a survey, writes it to IndexedDB, and enqueues a create operation. When online, the synchronization service processes queued work and updates the local survey and queue status. In this project version, the simulated server uses local storage and artificial request delay, so a successful sync demonstrates the application flow rather than delivery to a real remote database.

## 4. Evidence and Screenshots

No application runtime screenshots were found in the supplied project folder. The repository includes app artwork, but those assets do not demonstrate working screens. Screenshots should be captured from a running build before submission; they should not be replaced with mockups or described as tested evidence.

Recommended evidence to add:

1. **Survey form:** show the required fields and camera/location controls.
2. **Offline capture:** show a survey saved while disconnected and its pending status.
3. **Campus map or survey list:** show saved records and their facility severity/status.
4. **Synchronization view:** show a queued item and its resulting status after a connection is restored.

## 5. Technical Challenges and Resolutions

### Working without a stable connection

A field survey must remain usable when the device is offline. The app addresses this by persisting drafts and surveys in IndexedDB and recording mutations in a synchronization queue. Queue states distinguish pending, syncing, synced, and failed operations, making retry behavior visible to the user.

### Supporting browser and native device features

Camera and location capabilities differ between a browser and a native iOS build. The hardware service centralizes access through Capacitor plugins and provides browser fallbacks. This keeps the form workflow shared across environments, although actual permission prompts and device results still need testing on target devices.

### Location may be unavailable or imprecise

The source includes a fallback to the VKU campus center when the device denies or cannot provide location. That fallback prevents the workflow from stopping, but it is approximate and should not be treated as a measured survey location. The interface or submitted record should make the fallback clear to avoid misleading field data.

### Demonstrating synchronization without a production backend

The project currently simulates the server using local storage and artificial latency. This allows the queue, success, and failure paths to be demonstrated locally. Before production use, the mock service would need to be replaced with an authenticated API and server-side persistence; that backend integration is not present in the inspected source.

## 6. Conclusion

The inspected source implements the core structure of an offline-oriented campus facility survey PWA: local drafts and survey storage, a retryable synchronization queue, service-worker caching, survey list/map views, and Capacitor-based camera and location access. Its primary limitation is that synchronization currently targets a local mock server, and the supplied folder contains no runtime screenshots or verified demo link. The implementation should therefore be presented as a source-code review unless a device/browser run is completed and documented.

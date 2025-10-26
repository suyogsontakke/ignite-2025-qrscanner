<div align="center">
<!-- Use your logo URL -->
<img src="https://i.postimg.cc/HkXcxcgX/1758263167091.png" alt="IGNITE 2025 Logo" width="150" />
<h1>IGNITE 2025 - Ticket Verifier</h1>
<p>
A secure, mobile-friendly web application for event staff to scan and verify attendee QR code tickets in real-time for the IGNITE 2025 event.
</p>

<!-- GitHub badges -->

<p>
<a href="https://github.com/suyogsontakke/ignite-2025-qrscanner/releases"><img src="https://img.shields.io/github/v/release/suyogsontakke/ignite-2025-qrscanner?label=Latest%20release&style=flat-square" alt="Latest release"/></a>
    <a href="https://github.com/suyogsontakke/ignite-2025-qrscanner/stargazers"><img src="https://img.shields.io/github/stars/suyogsontakke/ignite-2025-qrscanner?style=flat-square" alt="Stars"/></a>
    <a href="https://github.com/suyogsontakke/ignite-2025-qrscanner/forks"><img src="https://img.shields.io/github/forks/suyogsontakke/ignite-2025-qrscanner?style=flat-square" alt="Fork"/></a>
    <a href="https://github.com/suyogsontakke/ignite-2025-qrscanner/blob/main/LICENSE"><img src="https://img.shields.io/github/license/suyogsontakke/ignite-2025-qrscanner?style=flat-square&color=blue" alt="License"/></a>
    <img src="https://img.shields.io/github/languages/top/suyogsontakke/ignite-2025-qrscanner?style=flat-square&logo=html5&logoColor=white&color=E34F26" alt="Top Language: HTML"/>
    <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs welcome"/>
</p>
</div>

<div align="center">
  <img src="https://i.postimg.cc/rFCSQ2Gz/image.png" alt="IGNITE 2025 Verifier Screenshot"/>
</div>

---

## 📖 Table of Contents

<details><summary>Table of Contents</summary>

* [🚀 About This Project](#-about-this-project)
* [✨ Key Features](#-key-features)
* [🛠️ Technologies Used](#️-technologies-used)
* [🔐 Security Model](#-security-model)
* [🧰 Getting Started](#-getting-started)
  * [📋 Prerequisites](#-prerequisites)
  * [⚙️ Installation & Setup](#️-installation--setup)
  * [▶️ Run Locally](#️-run-locally)
* [🔒 Configuration](#-configuration)
* [🚀 Deployment](#-deployment)
* [🤝 Contributing](#-contributing)
* [📄 License](#-license)
* [💎 Acknowledgements](#-acknowledgements)

</details>

---

## 🚀 About This Project

This repository contains the **Ticket Verifier** application for the "IGNITE 2025" event management platform. It's a secure, mobile-first web application designed for event staff (e.g., at the entrance) to quickly scan attendee QR codes and verify their entry status against the **Cloud Firestore** database.

Built with **Vanilla JavaScript**, **Tailwind CSS**, and leveraging the browser's camera API via `getUserMedia` and `jsQR` for scanning, this tool provides instant feedback on ticket validity. Access is restricted to authorized personnel using the same **Firebase Email/Password Authentication** as the Admin Dashboard.

The Verifier reads attendee data from the `approved_registrations` collection (populated by the Admin Dashboard) and updates the check-in status directly in Firestore, secured by specific rules.

---

## ✨ Key Features

* **Secure Admin Login:** Requires login using the same **Firebase Email/Password** credentials as the Admin Dashboard, ensuring only authorized staff can use the tool.
* **Real-time QR Code Scanning:** Utilizes the device camera (`getUserMedia`) and the `jsQR` library to continuously scan for QR codes.
* **Image File Scanning:** Allows uploading an image file containing a QR code for verification as an alternative to the live camera feed.
* **Firestore Integration:** Connects to the **Firebase Firestore** database to:
    * Fetch attendee data based on the scanned QR code's unique ID from the `approved_registrations` collection.
    * Check the current `checkedIn` status of the attendee.
    * Securely update the `checkedIn` status and `checkedInAt` timestamp upon successful verification.
* **Instant Feedback:** Provides clear visual feedback (Success, Already Checked In, Invalid Ticket) in a modal pop-up immediately after scanning.
* **Attendee Details Display:** Shows key attendee information (Name, Email, Ticket ID) upon successful verification or when identifying an already checked-in ticket.
* **Live Check-in Log:** Displays a real-time, reverse-chronological list of successfully checked-in attendees using Firestore `onSnapshot`.
* **Log Management:** Allows selecting checked-in attendees from the log and clearing their check-in status (setting `checkedIn` back to `false`) via a secure batch update, useful for correcting mistakes.
* **Mobile-First Design:** Optimized for use on mobile devices commonly used by event staff.

---

## 🛠️ Technologies Used

<details><summary>This project utilizes the following technologies and services:</summary>

* [**HTML5**](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5): Standard markup language for creating web pages.
* [**Tailwind CSS**](https://tailwindcss.com/): A utility-first CSS framework loaded via CDN for styling and responsiveness.
* [**JavaScript (ES6+)**](https://developer.mozilla.org/en-US/docs/Web/JavaScript): Core programming language for camera access, QR scanning logic, API calls, and DOM manipulation. Uses modern features like Modules.
* [**Firebase Firestore**](https://firebase.google.com/products/firestore): NoSQL cloud database used to read attendee status from `approved_registrations` and update check-in fields. Real-time listeners (`onSnapshot`) power the check-in log.
* [**Firebase Authentication**](https://firebase.google.com/products/auth): Used for **Email/Password Authentication** to restrict access to authorized event staff/admins only.
* [**jsQR**](https://github.com/cozmo/jsQR): JavaScript library for detecting and decoding QR codes from camera streams or images.
* [**WebRTC (`getUserMedia`)**](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia): Browser API used to access the device camera for live scanning.

</details>

[![Technologies Used](https://skillicons.dev/icons?i=html,tailwind,js,firebase)](https://skillicons.dev)

---

## 🔐 Security Model

This Verifier application is designed with security as a priority for access control at the event:

1.  **Mandatory Admin Login:** The application is completely inaccessible until a user authenticates using the designated admin **Firebase Email/Password** credentials. This prevents unauthorized individuals from verifying tickets or viewing attendee data.
2.  **Firestore Security Rules:** Backend database operations are strictly controlled:
    * Only authenticated users whose **UID matches the designated admin UID** can `read` from the `approved_registrations` collection.
    * Only authenticated admins can `update` documents in `approved_registrations`, and crucially, the rules restrict updates *only* to the `checkedIn` and `checkedInAt` fields. This prevents accidental or malicious modification of attendee names, emails, etc., during the check-in process.
    * Anonymous users or non-admins **cannot** read attendee data or perform check-ins. *(See `FIRESTORE.rules` file for details)*.

---

## 🧰 Getting Started

To get the Ticket Verifier running locally for testing or development.

### 📋 Prerequisites

* You **must** have already set up the Firebase project, Firestore database, and Admin Authentication account as described in the Admin Dashboard setup.
* **Firebase Project:** Access to your existing Firebase project console with Firestore and Authentication configured.
* **Admin Account:** The email and password for the admin user created in **Firebase Authentication**.
* **HTTPS:** `getUserMedia` (camera access) typically requires a secure context (HTTPS). Running via `localhost` usually works, but deploying or using tunneling tools might require HTTPS setup.
* [Node.js](https://nodejs.org/en/) (Recommended: for using `live-server` which often handles HTTPS better)
* [Git](https://git-scm.com/downloads)

### ⚙️ Installation & Setup

1.  **Clone the Repo**
    
    ```bash
    git clone https://github.com/suyogsontakke/ignite-2025-qrscanner.git
    cd ignite-2025-qrscanner
    ```

2.  **Configure Firestore Rules**
    * Ensure the **unified Firestore rules** (provided in `FIRESTORE.rules`) are active in your Firebase project. This includes setting `YOUR_ADMIN_UID_HERE` correctly.

3.  **Configure `verifier.html`**
    * Open `verifier.html` in a code editor.
    * Find the `<script type="module">` section.
    * **Firebase Config:** Locate the `firebaseConfig` object and ensure it matches the config from your Firebase project settings (copy it from your `admin.html` or `index.html`).
    * **Admin Email:** Find the `loginForm.addEventListener` function. Set the `email` variable to the email address of the admin account you created in Firebase Authentication.
        ```javascript
        // --- !!! SET YOUR ADMIN EMAIL HERE !!! ---
        const email = "your-admin-email@example.com";
        ```

### ▶️ Run Locally

* **Simple Method:** Open the `verifier.html` file directly in your web browser (might have issues with camera access if not served via HTTPS/localhost).
* **Recommended Method (using a local server):**
    ```bash
    # Make sure you are in the project directory
    npx live-server
    # OR if installed globally:
    # live-server
    ```
    Navigate to the local URL provided (e.g., `http://127.0.0.1:8080`). You will be prompted to log in.

---

## 🔒 Configuration

* **Firebase Configuration Object:** Found within the `<script type="module">` in `verifier.html`. Must match your Firebase project settings.
* **Admin Email Address:** Hardcoded within the `loginForm.addEventListener` function in `verifier.html`. This must match an existing Email/Password user in your Firebase Authentication.

**Note:** The admin password is *not* stored in the code; it is entered by the user and verified by Firebase Authentication directly.

---

## 🚀 Deployment

Deploy this static HTML file. Ensure it's hosted securely (HTTPS is generally required for camera access). Restrict access appropriately as this is an internal tool for event staff.

* **Firebase Hosting:** Recommended, integrates well.
* **Vercel/Netlify:** Possible, consider adding password protection at the hosting level if available.
* **Internal Server/Private Network:** Suitable for restricted access.

```bash
# Example Firebase Deployment
firebase deploy --only hosting
```

## 🤝 Contributing

Contributions are welcome! Please follow standard fork-and-pull-request workflow.

1. Fork the Project

2. Create your Feature Branch (git checkout -b feature/AmazingVerifierFeature)

3. Commit your Changes (git commit -m 'Add some AmazingVerifierFeature')

4. Push to the Branch (git push origin feature/AmazingVerifierFeature)

5. Open a Pull Request

## 📄 License

Distributed under the MIT License. See LICENSE file for more information.

## 💎 Acknowledgements

● Firebase

● Tailwind CSS

● jsQR

● WebRTC APIs

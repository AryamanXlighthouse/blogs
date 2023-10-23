# Unlocking Decentralized File Verification: A Comprehensive Guide to Implementing Document Verification System with Lighthouse SDK

## Overview
This comprehensive guide will walk you through the steps to implement a Document Verification System using the Lighthouse SDK. This application allows users to upload important documents, which are then timestamped and stored immutably on the Filecoin network for verification purposes.

### Prerequisites
- Basic understanding of JavaScript and Node.js
- **Node.js and NPM:** If not already installed, you can download and install them from [here](https://nodejs.org/).
- **Lighthouse CLI:** Installed globally on your machine. Install it via npm using the command:
    ```bash
    npm install -g @lighthouse-web3/sdk
    ```
- **API Key:** Generate it using Lighthouse CLI or refer to the official [documentation](https://docs.lighthouse.storage/) for more methods.


## Introduction to Lighthouse

Lighthouse is a perpetual file storage protocol designed to merge the robust storage capabilities of IPFS and Filecoin with the adaptability of various blockchain networks. By enabling users to pay a one-time fee for long-term storage, it presents a cost-effective alternative to the recurring expenses tied to conventional cloud storage, striving to deliver a secure and decentralized storage solution.

## Key Concepts

### IPFS (InterPlanetary File System)
IPFS is a protocol designed to make the web faster, safer, and more open by replacing the traditional, location-based address system with content-based addressing. This means files and content are addressed by what they contain, not where they are located, ensuring decentralization and security.

### Filecoin
Filecoin is a decentralized storage network that turns cloud storage into an algorithmic market. It facilitates the trading of excess storage space, allowing users to rent their extra space out, creating a decentralized market for data storage and retrieval.

### PoDSI (Proof of Data Segment Inclusion)
PoDSI is a proof mechanism that confirms a specific piece of data is included and stored within the Filecoin network. It acts as a certificate of authenticity, verifying the existence, integrity, and timestamp of stored files.


## Step-by-Step Implementation

#### Step 0: Generate API Key using CLI

With the Lighthouse CLI installed, follow these steps to generate an API key:

1. **Check if a Wallet is Available:**
    ```bash
    lighthouse-web3 wallet
    ```
    If there is no wallet, proceed to the next step to create or import one.

2. **Create a New Wallet (if none exists):**
    ```bash
    lighthouse-web3 create-wallet
    ```

   **Or Import an Existing Wallet:**
    ```bash
    lighthouse-web3 import-wallet --key <private_key>
    ```
    Replace `<private_key>` with your actual private key.

3. **Generate a New API Key:**
    ```bash
    lighthouse-web3 api-key --new
    ```

   This command will return an API key that you can use to interact with the Lighthouse storage system.


### Step 1: Upload a Document
Use the Lighthouse SDK to upload a document to the Filecoin network.
```javascript
import lighthouse from "@lighthouse-web3/sdk";

async function uploadDocument() {
    const filePath = '/path/to/your/document.pdf';
    const apiKey = 'YOUR_API_KEY';

    const uploadResponse = await lighthouse.upload(filePath, apiKey);
    console.log('File Uploaded, CID:', uploadResponse.cid);
}

uploadDocument().catch(console.error);
```

### Step 2: Retrieve PoDSI (Proof of Data Segment Inclusion)
PoDSI certifies the existence and integrity of the document on the Filecoin network at a specific time.
```javascript
import axios from "axios";

async function getPoDSI(cid) {
    const response = await axios.get(`https://api.lighthouse.storage/api/lighthouse/get_proof?cid=${cid}`);
    console.log('PoDSI:', response.data);
}

const cid = 'Qm...';  // Replace with the actual CID of your file
getPoDSI(cid).catch(console.error);
```

### Step 3: Verify the Document
Verify the document's authenticity using its PoDSI.
```javascript
function verifyDocument(poDSI) {
    const { pieceCID, dealInfo } = poDSI;

    if (!pieceCID || !dealInfo || dealInfo.length === 0 || !dealInfo.every(deal => deal.dealId && deal.storageProvider)) {
        console.error('Verification Failed');
        return false;
    }

    console.log('Document Verified:', pieceCID);
    return true;
}

getPoDSI(cid).then(verifyDocument).catch(console.error);
```

### Step 4: Download the Document
Enable document retrieval using the document's CID.
```javascript
async function downloadDocument(cid) {
    const response = await axios({
        method: 'GET',
        url: `https://gateway.lighthouse.storage/ipfs/${cid}`,
        responseType: 'stream',
    });

    // Implement logic to save the file stream to a local file
}

downloadDocument(cid).catch(console.error);
```

## Conclusion
Congratulations! You’ve now set up a basic Document Verification System using the Lighthouse SDK and Filecoin network. This walkthrough can be extended and customized to meet specific use case requirements and additional functionalities. Dive in, explore, and enhance your application’s capabilities!

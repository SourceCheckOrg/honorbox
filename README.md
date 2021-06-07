# honorbox

## Introduction

The HonorBox "platform" gives publishers of content three powerful capabilities:
1. To create a "donation address" specific to each piece of content, such that donations or "honor system" payments can be made by end-consumers regardless of distribution.
2. To embed in each publication to verifiable claims about its authenticity and the royalty commitments its publisher assumes, including for direct donations.
3. To embed a QR code from #1 into #2, thus guaranteeing that royalty statements can be automatically and programmatically enforced by decentralized direct payments.

A demo of the backend can be found [here](https://backend-demo.honorbox.network/)

## Technological/Stack choices

"Donation addresses" are encoded, in this prototype, as Solana smart contract, or technically as two Solana addresses, one for control and the other to hold balances for each withdrawer of funds authorized by the initial royalty contract.  This contract, along with authorship/rights claims, are encoded at time of publication using a back-end explained below. One by-product of this encoding is a QR code (generated using the npm package [QRCode](https://www.npmjs.com/package/qrcode)) which can be displayed on a donation/authenticity/royalties page. This optional page is appended to the PDF using [PDFKit](https://pdfkit.org/), which is also used to deterministically generate the "tamperproofing" hash contained in the authenticity statement.

Both sets of verifiable claims about a publication are encoded as Verifiable Credentials, a [W3C standard](https://www.w3.org/TR/vc-data-model/) (CR stage) that wraps a set of claims about a subject (expressable in, for example, a JSON object) in a signed JSON-LD envelope, which allows for data portability by anchoring to one or more Linked-Data Ontologies. We use the open-source [DIDKit](https://spruceid.dev/docs/didkit/) libraries from Spruce Systems to handle signing mechanics and to convert royalty statements, authenticity statements, and blockchain address/accounts into Verifiable Credentials.  These VCs are then embedded into PDFs using [PDFKit](https://pdfkit.org/).

All of this is handled in a custom back-end, which we are making available as open-source (linked below). It is built by extending Strapi using next.JS and TailwindUI.  One noteworthy feature is that rather than simply using Strapi's built-in username/password user account system, we bound Strapi accounts to an "SSI" wallet, which not only allows for user-friend KMS and blockchain interoperability, but also allows a copy of the VCs generated to be stored by the user for more secure record-keeping and privacy-preserving auditing. As the Spruce team finishes packaging the next version of Credible, we plan to migrate from the mobile-based to the browser-based version of Credible, although we might keep both options once a replication system is introduced.  Strictly speaking, there is nothing specific to Credible that makes it impossible to replace it with any other standards-conformant SSI wallet. 

PDFKit can also be used, in combination with DIDKit, to verify a tamperproof authenticity statement against the content. Such verification is out of scope for this prototype, but we have  already prototyped [such verification](https://github.com/SourceCheckOrg/wms-ssi-preview) as part of another project.

## Architecture and Dependencies

This new SourceCheck prototype builds on an earlier project made with support from a small Mozilla prototyping grant (see codebase [here](https://github.com/SourceCheckOrg/wms-ssi-prototype) and live demo [here](https://wms.sourcecheck.org/)). This earlier project stitched together open-source components (next.JS, Strapi, Spruce Systems' didkit and credible) to create a backend for publishers to embed authenticity provenance, tamper-proofing, and royalty-obligation transparency commitments into PDFs in the form of W3C [Verifiable Credentials](https://www.w3.org/TR/vc-data-model/) (another draft standard a little further along in W3C). These PDFs could then be view in a WMS-enabled server-side renderer extracting and verifying.

To understand how this functionality is composed of various open-source components, extensions, and modifications, see our [architecture document](architecture.md).  Also see our [authentication and signing flows](flows.md) document and our [changelog](CHANGELOG.md) for the project to round out the documentation.

The components, organized as separate repositories for maximum reusability and forking,  are:
* [Revenue-Sharing on Solana](https://github.com/SourceCheckOrg/revenue-sharing-solana): Code snippets for establishing simple revenue-sharing smart contracts according to arbitrary user input (as seen in the backend component)
* [HonorBox Publisher Back-end](https://github.com/SourceCheckOrg/honorbox-ui): A site where publishers can upload content in the form of a PDF, define verifiable royalty commitments, encode these as smart contracts on the Solana blockchain, and wrap all of this into a new PDF for "honor system" distribution.
* [HonorBox Back-End API](https://github.com/SourceCheckOrg/honorbox-api): Customized Strapi API to handle provenance, VC, and re-wrapping of tamper-proofed and signed PDFs
* [HonorBox Back-End dB](https://github.com/SourceCheckOrg/honorbox-db): MySQL and Redis dockerized to support Strapi & customizations
* [Credible Wallet](https://spruceid.dev/docs/credible/): Used for authentication and signing. Custom builds can be made from the [github repo](https://github.com/spruceid/credible/), and standard/demonstrator builds will soon be available in both major app stores (being listed at time of press).

## Updates

Sign up for updates on our website, [SourceCheck.org](https://sourcecheck.org/).
# Azeroth Survivors: iPhone build for AltStore

This small repository builds the [native iPhone port](https://radicle.network/nodes/iris.radicle.network/rad%3Az2StRLdKwdJTCgS1gj1NcZMtK22KQ) on a GitHub-hosted Mac. It produces an unsigned `.ipa` for AltStore Classic to sign and install using a physical Windows PC. It does not run the game on a server.

The workflow is self-contained: only `.github/workflows/build-ios.yml` is required. It fetches source commit `28c6e38cf1bab2509e29f67baebdcc3056758882` from Radicle, uses the upstream Rust toolchain and Cargo lockfile, and builds with Xcode 26.6 on `macos-26`. It uses the neutral icon shipped by the port and does not download or upload WoW assets. No Apple ID, signing certificates, GitHub secrets or paid Apple membership are needed for the build.

## Run the build

1. Put `.github/workflows/build-ios.yml` in your GitHub repository's default branch, preserving that exact path. In GitHub's browser editor you can use **Add file > Create new file**, enter the path, and paste the YAML contents. Do not upload only this ZIP: Actions needs the actual YAML file.
2. Open **Actions > Build iPhone IPA for AltStore > Run workflow**.
3. Wait for the build to finish. A first Rust/Bevy build can take a long time; the job allows up to three hours. You can close your browser while GitHub builds it.
4. Open the completed run and download the **AzerothSurvivors-AltStore** artifact. You must be signed into GitHub. Artifacts are retained for 14 days; save the IPA locally.
5. Extract the download ZIP. The file to install is **AzerothSurvivors-unsigned.ipa**, not the outer ZIP. The package also includes `INSTALL.txt`, build versions, licenses and a checksum.

Standard hosted runners are free for public repositories under GitHub's current policy. Private repositories use your plan's Actions allowance and may incur charges. This workflow runs only when manually started.

## Install using your physical Windows PC

Follow [AltStore Classic's Windows installation instructions](https://faq.altstore.io/altstore-classic/how-to-install-altstore-windows). Install the specific Apple components it requests; do not substitute Microsoft Store iTunes/iCloud versions if the guide says otherwise. AltStore Classic is the sideloading tool used here, not AltStore PAL.

Connect and trust your iPhone, install AltStore using AltServer, and enable iOS Developer Mode if required. Transfer the IPA to the iPhone's Files app (for example through iCloud Drive), then choose **AltStore > My Apps > +** and select it while AltServer is reachable on your PC. AltStore signs the app with your Apple account. Your Apple credentials are not entered into GitHub.

The game requires a separate English WoW 1.12.1 (build 5875) data set. The port's file-sharing support exposes its Documents directory. Use Windows Apple file sharing to copy the `.MPQ` files, then arrange them in the iPhone Files app as:

```text
On My iPhone/
  Azeroth Survivors/
    Data/
      <WoW 1.12.1 archives>.MPQ
```

Use **Apple Devices > your iPhone > Files > Azeroth Survivors**, or the corresponding iTunes File Sharing screen when using the Apple stack required by AltServer. If the app folder does not appear, launch the game once, close it, and reconnect the phone. Files copied to the top level can be moved into `Data` with the iPhone Files app. Do not nest the files inside `Data/Data`. The port recommends approximately 6 GB free.

The supplied Windows game bundle in the VPS has a repacked patch-only data set. Its compatibility with this community iPhone port has **not** been verified; do not assume it replaces the complete 1.12.1 data set required by upstream.

Free-account apps need refreshing within seven days using AltServer on the physical PC. Refreshing does not require a new GitHub build. Keep the same app and account when refreshing; deleting the app deletes its stored game data. No computer needs to run during gameplay.

## Validation and troubleshooting

CI checks the Xcode build result, arm64 architecture, iPhoneOS platform (not Simulator), file-sharing settings, IPA ZIP integrity, and absence of bundled `.MPQ` files. It saves build logs even after a failed compilation. The engine is compiled natively and does not require enabling JIT.

The workflow has to complete on GitHub before an IPA exists. Successful packaging alone does not prove installation or gameplay: those must be tested on the iPhone. If the build fails, download **iphone-build-logs**. If AltStore rejects the app, retain its exact error message. If the game launches but cannot load a zone, check the data set and folder layout first.

Changing source revision or Xcode version requires reviewing the port's build script and dependencies again. The one CI adjustment to upstream is adding `--locked` to its Cargo invocation; game and touch-control code are unchanged.

## Credits

Game by Aethus; desktop reconstruction by OutThisLife and the benilla/wenilla contributors; iOS port from the linked Radicle project. Engine licenses remain MIT or Apache-2.0, as provided upstream. Game data is supplied separately by the player.

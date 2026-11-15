# SpaceEngineers-SurroundSound-Fix
Fixes inverted 5.1 / 7.1 surround sound channels in Space Engineers (runtime Harmony patch via AppDomainManager
# Space Engineers — 5.1 / 7.1 Surround Sound Channel Fix

After years of dealing with broken 5.1 / 7.1 surround sound in Space Engineers and spending countless hours digging through the audio engine, this runtime patch finally fixes positional audio without modifying any original game files or requiring Steam / third-party plugin loaders.

## The Issue
In `VRage.Audio.dll` (`MyXAudio2.Update`), listener orientation vectors are passed from VRage's right-handed coordinate system to XAudio2's left-handed coordinate system without proper conversion. On 5.1 and 7.1 surround setups, this causes **Front/Rear** and **Left/Right** channels to be inverted.

Because `VRage.Audio.dll` is Strong-Name signed and mixed-mode, editing it on disk causes an immediate crash on startup.

## How It Works
This fix uses Windows' built-in .NET Framework `AppDomainManager` hook (`APPDOMAIN_MANAGER_ASM`) and **Harmony** (`0Harmony.dll`) to intercept `MyXAudio2.Update` in memory at launch and invert `listenerFront` and `listenerUp` on the fly.

---

## Quick Installation (Pre-compiled)

1. Download **`AudioFix.dll`** and **`Start_SE_AudioFix.bat`** from this repository.
2. Download the latest **Harmony** release from GitHub (`pardeike/Harmony` -> `Harmony-Fat.x.x.x.x.zip`) and extract **`0Harmony.dll`** from the `net472` (or `net48`) folder.
3. Place all three files directly into your game's **`Bin64`** folder (next to `SpaceEngineers.exe`):
   * `AudioFix.dll`
   * `Start_SE_AudioFix.bat`
   * `0Harmony.dll`
4. Launch the game using **`Start_SE_AudioFix.bat`** (you can create a desktop shortcut to it).

---

## Building from Source (Optional)
If you prefer to compile `AudioFix.cs` yourself using the built-in Windows C# compiler, run in `cmd` (replace `YOUR_GAME_PATH\Bin64` with your actual path):

```cmd
C:\Windows\Microsoft.NET\Framework64\v4.\(0.30319\csc.\)exe /target:library /out:"YOUR_GAME_PATH\Bin64\AudioFix.dll" /r:"C:\Windows\Microsoft.NET\Framework64\v4.0.30319\netstandard.dll" /r:"C:\Windows\Microsoft.NET\Framework64\v4.0.30319\System.Runtime.dll" /r:"YOUR_GAME_PATH\Bin64\0Harmony.dll" /r:"YOUR_GAME_PATH\Bin64\VRage.Math.dll" AudioFix.cs
```

---

*Special thanks to Google Gemini and ChatGPT for helping debug the vector math and put this runtime patch together.*

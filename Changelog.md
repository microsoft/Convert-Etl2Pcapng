2025.05.001 Update:

- Added SUPPORT.MD
- (GitHub Issue 14/15) Changed the module save path to $ENV:LOCALAPPDATA from the module location to prevent issues with OneDrive.
  - Added logic to migrate existing settings from the module to LOCALAPPDATA.
  - Moved etl2pcapng from the \etl2pcapng subdirectory to the app root dir.
- Added a script-scoped settings ($script:settings) variable to reduce file system IO.
  - Updated various functions for script:settings to work.
  - Better settings update logic to catch directory changes caused by the version update.
- Set-E2PSettings automatically updates script:settings to catch changes.
- Added -NoClobber to Unregister-Etl2Pcapng, which prevents the deletion of settings.xml and etl2pcapng.exe.
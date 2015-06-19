---
title: Mac Finder preferences
---

* [Set prefered Layout](#set-prefered-layout)

## Set prefered Layout

There are four different layouts that you can choose in the Finder.

* Cover Flow Layout: **Flwv**
* List View: **Nlsv**
* Column View: **clmv**
* Icon View: **icnv**

```
defaults write com.apple.Finder FXPreferredViewStyle clmv
```
Where _clmv_ is the layout name.

Restar finder to take effects changes

```
killall Finder
```

Reset layout settings.
```
defaults write com.apple.Finder FXPreferredViewStyle icnv
```
 
 
# Set item arrangement to none (enables folder dropdowns, 'Name' if you want to remove them)
defaults write com.apple.finder FXPreferredGroupBy -string "None"
 
# Sort list view by kind in ascending order (Windows style)
/usr/libexec/PlistBuddy -c "Set :StandardViewSettings:ExtendedListViewSettings:sortColumn kind" ~/Library/Preferences/com.apple.finder.plist
/usr/libexec/PlistBuddy -c "Set :StandardViewSettings:ExtendedListViewSettings:columns:4:ascending true" ~/Library/Preferences/com.apple.finder.plist
/usr/libexec/PlistBuddy -c "Set :StandardViewSettings:ListViewSettings:sortColumn kind" ~/Library/Preferences/com.apple.finder.plist
/usr/libexec/PlistBuddy -c "Set :StandardViewSettings:ListViewSettings:columns:kind:ascending true" ~/Library/Preferences/com.apple.finder.plist
 
# Sort Folders before files hack (Windows style)
FILE=/System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings
# Backup InfoPlist.strings first if no backup exists
[ -f $FILE.bak ] || sudo ditto $FILE $FILE.bak
# Convert InfoPlist.strings to XML
sudo plutil -convert xml1 $FILE
# Add a space in front of 'Folder' string
sudo sed -i '' 's/g\>Folder/g\> Folder/' $FILE > /dev/null
# Convert InfoPlist.strings back to binary
sudo plutil -convert binary1 $FILE
 
# Remove all .DS_Store files to reset folder specific view options
sudo find / -name ".DS_Store" -depth -exec rm -f {} \;
 
# Restart cfprefsd and Finder
killAll cfprefsd
killAll Finder

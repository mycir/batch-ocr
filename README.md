# batch-ocr

Bash wrapper script around command line scans and OCR driven by [Zenity](https://help.gnome.org/users/zenity/stable/index.html.en) dialogs. Essentially for X11 but see [Wayland possibilities](#wayland-possibilities).

Intended for small, unchallenging jobs where conversion of scanned pages to searchable PDF, text or hocr files is required. Key objectives, simplicity and speedy workflow. [More...](#more-information)</br></br>


![demo](screenshots/demo.gif)

## More information

Targets budget airscan/eSCL scanners but there is scope for other devices by creating a '\<scanner device name\>.options' file, see [sample](./samples/epson.options).

Initial settings are applied from a '<scanner device name\>.scan-options' file (see [sample](<./samples/airscan:e0:Canon TS3355 Scanner.scan-options>)) if it exists in the same folder as the script or a previously started job folder. If not, and XSane is installed*, they are applied from any ~/.sane/xsane/*.drc that includes a match with the selected scanner's model name. (In the case of scanners that don't expose gamma, brightness and contrast options, these settings are applied during image conversion.)

Chosen values and output options are saved in a 'job-settings' file, which is read and applied if a job is aborted, then resumed. On resume, the options are to continue scanning at next index value (or revert it to rescan earlier pages) or wipe the output folder.

Note that the crop dialog accepts negative values. For example, if a double page split is significantly to the right of centre, enter a negative value for 'Left page - spine shadow' and a correspondingly greater value for 'Right page - spine shadow'.

In the case of text file output, setting 'OCR layout mode' to column applies a rudimentary column layout to results. (True layout detection is a vast field, now incorporating AI, and beyond the scope of this project.)

There is some limited sanitisation of user typos (strip alpha characters from numeric fields) but don't expect miracles.

Zenity doesn't allow for presetting control selections or text. With Zenity combos, this is achieved by shuffling the current option to the top of combo values to be displayed. For 'entry', i.e. edit controls, these are filled by sending keys from xdotool - not pretty and sensitive to system specs. If values aren't filled in correctly  from 'job_settings', or focus doesn't return to first control in dialog, increase the delay allowed for combo animation thus:</br>
```BOCR_XDOTOOL_DELAY=<milliseconds> ./batch-ocr```

The Zenity filepicker for the output folder defaults to $HOME. To change:</br>
```BOCR_FILEPICKER_START="<path to scans>" ./batch-ocr```

If modifying the script, some debug information can be displayed to stdout thus:</br>
```BOCR_DEBUG_INFO=1 ./batch-ocr```

These environment variables can be combined, e.g.</br>
```BOCR_XDOTOOL_DELAY=20 BOCR_FILEPICKER_START="$HOME/Scans" BOCR_DEBUG_INFO=1 ./batch-ocr```


## Dependencies

scanimage (sane-utils) - command line interface for accessing scanners.</br>
awk - for processing command output (only Unix awk constructs are used, for compatibility with other flavours).</br>
wmctrl (wmctrl) - query the window manager for information, and execute window management actions.</br>
xwininfo (xwininfo) - for querying window properties.</br>
xprop (x11-utils) - for querying GTK frame extents.</br>
xdotool (xdotool) - for simulating keyboard input.</br>
convert (ImageMagick) - for the creation, modification and display of bitmap images.</br>
zenity - Zenity is a program to display GTK+ dialogs.</br>
eog - Eye of GNOME graphics viewer program.</br>
tesseract (tesseract-ocr) - Command-line OCR engine.</br>
gs (ghostscript) - for converting PDFs to chosen paper size.</br>
pdftotext (poppler[-utils|-tools]) - for converting temporary PDF files to text, when column layout is required.

\* Optional:

xsane - scanner frontend for SANE (for tinkering around with gamma, brightness and contrast).

Note: **batch-ocr** checks for missing dependencies at startup and writes a suggested command line for their installation to stdout.

## Wayland possibilities

None known under Fedora >= 42. Otherwise:

Select 'Gnome on Xorg' at login.

or

```GDK_BACKEND=x11 ./batch-ocr```

## Testing

**batch-ocr** was developed under Kubuntu 24.04 LTS but has been tested successfully under the following VMs:

Fedora 40 (Gnome)</br>
Xubuntu2504 (XFCE)</br>
OpenSuse Leap 15.6 (KDE Plasma)

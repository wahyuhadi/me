# Fixing Size apps Java DWM
 ---
 &nbsp;
 ---
<p style="text-align: justify; letter-spacing: 0.002em;">
pada saat membuka applikasi desktop berbasis java pada DWM, sering kali mendapatkan issue mengenai size dari applikasi yang tak sesuai, lantas bagaimana cara fixing nya : sebanarnya cukup simple hanya dengan menambahkan export _JAVA_AWT_WM_NONREPARENTING=1 pada file .xinitrc
sebagai contoh1
</p>

```sh
export _JAVA_AWT_WM_NONREPARENTING=1
xrandr --output HDMI-1 --mode 1920x1080 &
nitrogen --restore &
picom -f &
exec dwm

```  


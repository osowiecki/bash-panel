bash-panel
==========

panel / application launcher written in bash

The goal of the project is to create a fully functional application launcher / tray operated by mouse within X environment.



#!/bin/bash

if [[ $UID != 0 ]]; then
    echo "Please run this script with sudo:"
    echo "sudo $0 $*"
    exit 1
fi

xdotool getactivewindow windowsize 700 60 windowmove 0 510

printat() ## USAGE: printat ROW COLUMN
{
    printf "${ESC}[${1};${2}H"
}

print_buttons()
{
   num_but=$#
   COLUMNS=${COLUMNS:-$(tput cols)}
   gutter=2
   gutters=$(( $num_but))
   but_width=$(( ($COLUMNS - ($gutters * $gutter)) / $num_but))
   
    
   
   n=0

   for but_str
   do
     col=$(( $gutter + ($n * ($but_width + $gutter)) ))
     printat $but_row $col
     printf "${ESC}[7m%${but_width}s" " "
     printat $but_row $(( $col + ($but_width - ${#but_str}) / 2 ))
     printf "%.${but_width}s${ESC}[0m" "$but_str"
     n=$(( $n + 1 ))
   done
}

ESC="" ##  A literal escape character
but_row=1

clear
mv=9  ## mv=1000 for press and release reporting; mv=9 for press only

_STTY=$(stty -g)      ## Save current terminal setup
stty -echo -icanon    ## Turn off line buffering
printf "${ESC}[?${mv}h        "   ## Turn on mouse reporting
printf "${ESC}[?25l"  ## Turn off cursor

audio=$(amixer | grep "Mono: Playback" | awk '{print $4}')
cpu_gov=$(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor);





while :
do


  [ $mv -eq 9 ] && mv_str=$(echo -e '\E[47;34m'"CPU ""$cpu_gov")
  
  [ $mv -eq 9 ] && mv_str2=$(echo -e '\E[47;35m'"$audio")

print_buttons "$mv_str" "$(echo -e '\E[1;42m'"EXIT")" "A $mv_str2"

  x=$(dd bs=1 count=6 2>/dev/null) ## Read six characters

  m1=${x#???}    ## Remove the first 3 characters
  m2=${x#????}   ## Remove the first 4 characters
  m3=${x#?????}  ## Remove the first 5 characters

  ## Convert to characters to decimal values
  eval "$(printf "mb=%d mx=%d my=%d" "'$m1" "'$m2" "'$m3")"

  ## Values > 127 are signed
  [ $mx -lt 0 ] && MOUSEX=$(( 223 + $mx )) || MOUSEX=$(( $mx - 32 ))
  [ $my -lt 0 ] && MOUSEY=$(( 223 + $my )) || MOUSEY=$(( $my - 32 ))

  ## Button pressed is in first 2 bits; use bitwise AND
  BUTTON=$(( ($mb & 3) + 1 ))

  case $MOUSEY in
       $but_row) ## Calculate which on-screen button has been pressed
                 button=$(( ($MOUSEX - $gutter) / $but_width + 1 ))
                 case $button in
                      1) printf "${ESC}[?${mv}l"
                         [ $mv -eq 9 ] && mv=1000 || mv=9
                         
  		 

###########################################################


governor=$(zenity --title="CPU Frequency Selector" --height=220 --text "Please select a CPU frequnecy governor. \n(Recommended option is Ondemand)" --list --separator=" & " --radiolist --column="tick" --column=option powersave "powersave" ondemand "ondemand" conservative "conservative" performance "performance")

for CPUFREQ in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
do
	[ -f $CPUFREQ ] || continue
	echo -n $governor > $CPUFREQ
done


cpu_gov=$(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor);

clear;
mv_str=$(echo -e '\E[47;34m'"CPU ""$cpu_gov")
mv_str2=$(echo -en '\E[47;35m'"$audio")
print_buttons "$mv_str " " $(echo -e '\E[1;42m'"EXIT")" "A $mv_str2 "








printf "${ESC}[?${mv}h";

###########################################################










                         [ $mv -eq 1000 ] && x=$(dd bs=1 count=6 2>/dev/null)
                         ;;
                      2) break ;;
		      
			
			

		      3) printf "${ESC}[?${mv}l"

[ $mv -eq 9 ] && mv=1000 || mv=9;
z='xdotool getactivewindow';

xterm alsamixer;
printf "${ESC}[?${mv}h";
audio=$(amixer | grep "Mono: Playback" | awk '{print $4}');
xdotool windowactivate $z;

mv_str=$(echo -e '\E[47;34m'"CPU ""$cpu_gov")
mv_str2=$(echo -en '\E[47;35m'"$audio")
clear;
print_buttons "$mv_str " " $(echo -e '\E[1;42m'"EXIT")" "A $mv_str2 "

                         [ $mv -eq 1000 ] && x=$(dd bs=1 count=6 2>/dev/null)
			  ;;

                 esac
                 ;;
       *) printat $MOUSEY $MOUSEX
          printf "X=%d Y=%d [%d]  " $MOUSEX $MOUSEY $BUTTON
          ;;
  esac

done

printf "${ESC}[?${mv}l"  ## Turn off mouse reporting
stty "$_STTY"            ## Restore terminal settings
printf "${ESC}[?12l${ESC}[?25h" ## Turn cursor back on
printf "\n${ESC}[0J\n"   ## Clear from cursor to bottom of screen


exit

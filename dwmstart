#! /bin/bash
# dwm status bar script

# How often to update the status bar
POLLDELAY=0.5
# Battery low threshold
BATTERYLOW=90

# Get network down/up speeds
ip()
(
	ip=$(ifconfig eth1| grep "inet A"|cut -d: -f2 | cut -d" " -f 1)
	echo "eth1:${ip}"
)

# Status bar time and date in the user's locale
timedate()
(	
	echo $(date +"%c")
)

# Print battery status, charge and time remaining
battery()
(
	# Get the battery string
	returnstring=$(acpi -b | awk '{sub(/,/, ""); sub(/%,/, "%"); printf "%s %s (%s %s %s)", $3,$4,$5,$6,$7,$8}' | sed 's/ )/)/g')
	powerlevel=$(echo $returnstring | awk '{ sub(/%/, ""); print $2 }')
	status=$(echo $returnstring | awk '{ print $1 }')

	# TODO add battery low warning
	echo $returnstring
)

# Get CPU temps
temp()
(
	echo $(sensors | awk '/Core/ {print $3}' | sed 's/[^a-zA-Z0-9.: ]//g' | tr '\n' ' ')
)

# Get CPU frequency
cpufreq()
(
	echo $(cpufreq-info -f -m)
)

# Get RAM info
memory()
(
	echo $(free | awk '/Mem/ {printf "%d MB/%d MB (%d MB free)\n", $3 / 1024.0, $2 / 1024.0, $4 / 1024.0 }')
)

# Get volume level
volume()
(
	echo $(amixer get Master playback | awk '/Mono:/ { print $4 }')
)

# Kill any rogue .xsession processes
for i in $(pidof -x $0)
do
	if [ "$i" -ne "$$" ]; then
		kill -9 $i
		killall "sleep"
	fi
done

# Start sound drivers
start-pulseaudio-x11 &

# Display the status bar
while true ; do
	xsetroot -name "$(memory)  |  $(cpufreq)  |  $(temp)  |  $(battery)  |  $(timedate)"
	sleep $POLLDELAY
done &

# Start dwm
exec /usr/local/bin/dwm

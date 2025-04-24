 # Linux-System-Health-Dashboard

# Bash Script: linux_dashboard.sh
#!/bin/bash
INTERVAL=5  # refresh interval in seconds

while true; do
  clear
  echo "==================== RHEL 9.5 SYSTEM HEALTH DASHBOARD ===================="
  echo "Time       : $(date)"
  echo "Hostname   : $(hostname)"
  echo "Uptime     : $(uptime -p)"
  echo "Kernel     : $(uname -r)"
  echo "----------------------------------------------------------------------"
  
  echo " CPU Load :"
  uptime | awk -F'load average: ' '{ print "Load Averages: " $2 }'
  echo

  echo " Memory Usage :"
  free -h | awk 'NR==2{ printf "Used: %s / Total: %s (%.2f%%)\n", $3, $2, $3*100/$2 }'
  echo

  echo "  Disk Usage :"
  df -h --output=source,size,used,avail,pcent,target | grep -v tmpfs
  echo

  echo " Network Traffic (vnstat):"
  vnstat | grep -A 5 "rx / tx"
  echo

  echo " Top 5 Memory-Consuming Processes:"
  ps aux --sort=-%mem | awk 'NR<=6 {print $1, $2, $3, $4, $11}'
  echo "----------------------------------------------------------------------"
  echo "Next update in ${INTERVAL}s... (Ctrl+C to exit)"
  sleep $INTERVAL

  # Install Epel Release  
  sudo dnf install \
  https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

  # Start Service 
  sudo systemctl enable --now vnstat

  # Enable the CRB Repository 
  sudo crb enable 
  sudo dnf update
  sudo dnf install vnstat -y

  # Grant Permission for .sh
  chmod +x linux_dashboard.sh

  # Run it 
  ./linux_dashboard.sh





  
  


version: '3'
services:
  windows:
    container_name: windows_vps
    image: scottyhardy/docker-wine:latest
    restart: unless-stopped
    privileged: true
    environment:
      - USER_NAME=ADMIN
      - PASSWORD=admin@123
      - DISPLAY=:0
      - XVFB_RESOLUTION=1920x1080x24
      - XVFB_SCREEN=0
      - XVFB_DISPLAY=:0
      - CPU_CORES=64
      - MEMORY=8G
      - DISK_SIZE=400G
      - TZ=Asia/Ho_Chi_Minh
    volumes:
      - ./data:/home/wine/.wine
      - /tmp/.X11-unix:/tmp/.X11-unix
    ports:
      - "3389:3389"  # RDP port
      - "8006:8006"  # Web control
      - "8000:8000"  # Web interface
    command: >
      bash -c "
        echo 'Configuring Windows VPS with 64 CPU cores, 8GB RAM, and 400GB disk...' &&
        echo 'Setting up user ADMIN with password admin@123' &&
        xvfb-run --server-args='-screen 0 1920x1080x24' wine wineboot &&
        wine reg add 'HKEY_CURRENT_USER\\Software\\Wine\\Explorer' /v Desktop /d 'Default' /f &&
        wine reg add 'HKEY_CURRENT_USER\\Software\\Wine\\Explorer\\Desktops' /v Default /d '1920x1080' /f &&
        wine reg add 'HKEY_LOCAL_MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server' /v fDenyTSConnections /t REG_DWORD /d 0 /f &&
        wine reg add 'HKEY_LOCAL_MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server\\WinStations\\RDP-Tcp' /v UserAuthentication /t REG_DWORD /d 0 /f &&
        wine reg add 'HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon' /v DefaultUserName /t REG_SZ /d 'ADMIN' /f &&
        wine reg add 'HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon' /v DefaultPassword /t REG_SZ /d 'admin@123' /f &&
        wine reg add 'HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon' /v AutoAdminLogon /t REG_SZ /d '1' /f &&
        wine explorer &&
        python3 -m http.server 8000 &
        novnc --vnc localhost:5900 --listen 8006 &
        tail -f /dev/null
      "

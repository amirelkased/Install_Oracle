### `Step 1:` Download Oracle Database Express Edition

- [Downoad It First](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html)

![Sec1](https://camo.githubusercontent.com/76109812f3127b0f86940373897b04ac8943cb3c0f057f90046444480f61bafd/68747470733a2f2f692e696d6775722e636f6d2f77617856496d762e706e67?utm_source=pocket_mylist)

### `Step 2:` Instructions before install Oracle

1. Copy the downloaded file and paste it in home directory

2. Unzip using the command

   `unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip`

3. Install required packages using the command

   `sudo apt-get install alien libaio1 unixodbc`

4. Enter into the Disk1 folder using command

   `cd Disk1`

5. Convert RPM package format to DEB package format (that is used by Ubuntu) using the command

   `sudo alien --scripts -d oracle-xe-11.2.0-1.0.x86_64.rpm`

6. Create the required chkconfig script using the command

   `sudo pico /sbin/chkconfig`

7. The pico text editor is started and the commands are shown at the bottom of the screen. Now `copy` and `paste` the following into the file and save

   ```text
   #!/bin/bash
   # Oracle 11gR2 XE installer chkconfig hack for Ubuntu
   file=/etc/init.d/oracle-xe
   if [[ ! `tail -n1 $file | grep INIT` ]]; then
       echo >> $file
       echo '### BEGIN INIT INFO' >> $file
       echo '# Provides: OracleXE' >> $file
       echo '# Required-Start: $remote_fs $syslog' >> $file
       echo '# Required-Stop: $remote_fs $syslog' >> $file
       echo '# Default-Start: 2 3 4 5' >> $file
       echo '# Default-Stop: 0 1 6' >> $file
       echo '# Short-Description: Oracle 11g Express Edition' >> $file
       echo '### END INIT INFO' >> $file
   fi
   update-rc.d oracle-xe defaults 80 01
   ```

8. Change the permission of the chkconfig file using the command

   `sudo chmod 755 /sbin/chkconfig`

9. Set kernel parameters. Oracle 11gR2 XE requires additional kernel parameters which you need to set using the command

   `sudo pico /etc/sysctl.d/60-oracle.conf`

10. Copy the following into the file and save

    ```text
    # Oracle 11g XE kernel parameters
    fs.file-max=6815744
    net.ipv4.ip_local_port_range=9000 65000
    kernel.sem=250 32000 100 128
    kernel.shmmax=536870912
    ```

11. Verify the change using the command

    `sudo cat /etc/sysctl.d/60-oracle.conf`

12. You should see what you entered earlier. Now load the kernel parameters

    `sudo service procps start`

13. Verify the new parameters are loaded using

    `sudo sysctl -q fs.file-max`

    > You should see the file-max value that you entered earlier.

14. Set up /dev/shm mount point for Oracle. Create the following file using the command

    `sudo pico /etc/rc2.d/S01shm_load`

15. Copy the following into the file and save

    ```text
    #!/bin/sh
    case "$1" in
    start)
        mkdir /var/lock/subsys 2>/dev/null
        touch /var/lock/subsys/listener
        rm /dev/shm 2>/dev/null
        mkdir /dev/shm 2>/dev/null
    *)
        echo error
        exit 1
        ;;

    esac
    ```

16. Change the permissions of the file using the command

    `sudo chmod 755 /etc/rc2.d/S01shm_load`

17. Now execute the following commands

    ```bash
    sudo ln -s /usr/bin/awk /bin/awk
    sudo mkdir /var/lock/subsys
    sudo touch /var/lock/subsys/listener
    ```

18. Now, Reboot Your System

![Sec2](https://camo.githubusercontent.com/76109812f3127b0f86940373897b04ac8943cb3c0f057f90046444480f61bafd/68747470733a2f2f692e696d6775722e636f6d2f77617856496d762e706e67?utm_source=pocket_mylist)

### `Step 3:` Install Oracle

1. Install the oracle DBMS using the command

   `sudo dpkg --install oracle-xe_11.2.0-2_amd64.deb`

2. Configure Oracle using the command

   `sudo /etc/init.d/oracle-xe configure`

3. Setup environment variables by editting your .bashrc file

   `pico ~/.bashrc`

4. Add the following lines to the end of the file

   ```text
   export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe
   export ORACLE_SID=XE
   export NLS_LANG=`$ORACLE_HOME/bin/nls_lang.sh`
   export ORACLE_BASE=/u01/app/oracle
   export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
   export PATH=$ORACLE_HOME/bin:$PATH
   ```

5. Load the changes by executing your profile

   `. ~/.bashrc`

6. Start the Oracle 11gR2 XE

   `sudo service oracle-xe start`

7. Add user YOURUSERNAME to group dba using the command

   `sudo usermod -a -G dba YOURUSERNAME`

![Sec3](https://camo.githubusercontent.com/76109812f3127b0f86940373897b04ac8943cb3c0f057f90046444480f61bafd/68747470733a2f2f692e696d6775722e636f6d2f77617856496d762e706e67?utm_source=pocket_mylist)

## `Step 4:` Using the Oracle XE Command Shell

1. Start the Oracle XE 11gR2 server using the command

   `sudo service oracle-xe start`

2. Use cursor when writing a query

   `sudo apt install rlwrap`

3. Start command line shell as the system admin using the command

   `rlwrap sqlplus sys as sysdba`

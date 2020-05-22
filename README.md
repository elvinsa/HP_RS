# HP_RS
script for HP router and switch optimization

from netmiko import ConnectHandler

username = input ("Enter username: ")
password = input ("Enter password: ")


#file for ip adderss list, error occured
f = open('c:\\Users\\user\\desktop\\not_successed.txt', 'a')

#file where you place all device addresses
with open('c:\\Users\\user\\desktop\\ssh_conn_ips.txt', 'r') as devices:
    output = devices.readlines()
    for ip in output:
        if int(ip.strip()[-1]) > 1: #check ip address last octet, deciding it is router or switch
            try:
                switches = {
                    'device_type':'hp_procurve',
                    'ip': ip,
                    'username': username,
                    'password': password
                }
                with open('c:\\Users\\user\\desktop\\conf_file_sw.txt') as config_file_sw: #file with commands for switches
                    config_file_sw_read = config_file_sw.read().splitlines()
                net_connect = ConnectHandler(**switches)
                command_output = net_connect.send_config_set(config_file_sw_read)
                show_output = net_connect.send_command("show vlan")
                print(show_output)
                net_connect.disconnect()
            except:
                print("Failed connect to", ip)
                f.write(f'{ip}\n')
            
        else:
            try:
                routers = {
                    'device_type':'hp_comware',
                    'ip': ip,
                    'username': username,
                    'password': password
                }
                with open('c:\\Users\\user\\desktop\\conf_file_rt.txt') as config_file_rt: #file with commands for routers
                    config_file_rt_read = config_file_rt.read().splitlines()
                net_connect = ConnectHandler(**routers)
                command_output = net_connect.send_config_set(config_file_rt_read)
                show_output = net_connect.send_command("display current-configuration")
                print(show_output)
                net_connect.disconnect()
            except:
                print("Failed connect to", ip)
                f.write(f'{ip}\n')        
config_file_sw.close()
config_file_rt.close()
f.close()

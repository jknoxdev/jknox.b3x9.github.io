---
title: AD Port Mon. w/ Replit in 5mins
description: Active Directory Traffic Monitor in 5 minutes
date: 2024-09-19
scheduled: 2024-09-19
layout: layouts/post.njk
tags:
  - AD
  - Replit
---

### init
So, this is pretty crazy. I saw a twitter / x post by [@ammaar](https://twitter.com/ammaar/status/1834312398016074083?s=61&t=9N0j0YO4k-q5LiBtHXL_3w) saying that they created a 3d version of snake in a one minute 1-shot. 

Now, I don't know what this means for me as a budding cyber security professional, so I thought I'd try the same thing, but with using Active Direcotory as the source. 

Here is the [@Repplit](https://replit.com) prompt I used: 

> "create a python program that can look at local network traffic and see if there is active directory related traffic. on a gui window, make a grid for each of the standard protocols with a grey rectangle around them. if the encrypted ones are in use,, highlight them with green, and if they are detected as unencrypted highlight them with a red rectangular border"

about 20 minutes later with some initial testing, here's what it's come up with:
![image of the Active Directory Traffic Analyzer Prototype](https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/3c51f9cb-1bfc-4588-dec8-77349a943d00/public)

Now, that's impressive. 

I still have to test it on a LAN, probably in a VM network on my cloud servers; but wow. 

I've copied the files here if you'd like to check it out. 
I feel weird about posting this to my github, since there's not really that much that I did. I mean, there's a lot more that can be done with this to make it actually useful, but as a proof of concept, I think this is really pretty amazing. 

here are the main files in the program, I'm mostly just copying these in so I can read them on my phone later: 


##### gui.py
```python
import tkinter as tk
from tkinter import messagebox, ttk
from ad_protocols import AD_PROTOCOLS, is_encrypted

class ADMonitorGUI:
    def __init__(self, master, simulate_packet_callback, set_ip_filter_callback, set_port_range_filter_callback, clear_filters_callback):
        self.master = master
        self.protocol_frames = {}
        self.simulate_packet_callback = simulate_packet_callback
        self.set_ip_filter_callback = set_ip_filter_callback
        self.set_port_range_filter_callback = set_port_range_filter_callback
        self.clear_filters_callback = clear_filters_callback

        self.create_grid()
        self.create_simulate_button()
        self.create_filter_inputs()
        self.create_alert_display()

    def create_grid(self):
        for i, protocol in enumerate(AD_PROTOCOLS.keys()):
            row = i // 4
            col = i % 4
            frame = tk.Frame(self.master, width=100, height=50, bg='gray')
            frame.grid(row=row, column=col, padx=5, pady=5)
            frame.grid_propagate(False)
            
            label = tk.Label(frame, text=protocol, bg='gray')
            label.pack(expand=True)

            frame.bind("<Button-1>", lambda e, p=protocol: self.show_protocol_info(p))
            label.bind("<Button-1>", lambda e, p=protocol: self.show_protocol_info(p))

            self.protocol_frames[protocol] = frame

    def create_simulate_button(self):
        self.simulate_button = tk.Button(self.master, text="Simulate Traffic", command=self.simulate_traffic)
        self.simulate_button.grid(row=len(AD_PROTOCOLS)//4 + 1, column=0, columnspan=4, pady=10)

    def create_filter_inputs(self):
        filter_frame = tk.Frame(self.master)
        filter_frame.grid(row=len(AD_PROTOCOLS)//4 + 2, column=0, columnspan=4, pady=10)

        # IP Filter
        tk.Label(filter_frame, text="IP Filter:").grid(row=0, column=0)
        self.ip_filter_entry = tk.Entry(filter_frame)
        self.ip_filter_entry.grid(row=0, column=1)
        tk.Button(filter_frame, text="Apply IP Filter", command=self.apply_ip_filter).grid(row=0, column=2)

        # Port Range Filter
        tk.Label(filter_frame, text="Port Range:").grid(row=1, column=0)
        self.start_port_entry = tk.Entry(filter_frame, width=6)
        self.start_port_entry.grid(row=1, column=1, sticky="w")
        tk.Label(filter_frame, text="-").grid(row=1, column=1)
        self.end_port_entry = tk.Entry(filter_frame, width=6)
        self.end_port_entry.grid(row=1, column=1, sticky="e")
        tk.Button(filter_frame, text="Apply Port Filter", command=self.apply_port_filter).grid(row=1, column=2)

        # Clear Filters
        tk.Button(filter_frame, text="Clear Filters", command=self.clear_filters).grid(row=2, column=0, columnspan=3, pady=5)

    def create_alert_display(self):
        alert_frame = tk.Frame(self.master)
        alert_frame.grid(row=len(AD_PROTOCOLS)//4 + 3, column=0, columnspan=4, pady=10)

        tk.Label(alert_frame, text="Alerts:").grid(row=0, column=0, sticky="w")
        self.alert_text = tk.Text(alert_frame, height=5, width=50)
        self.alert_text.grid(row=1, column=0, padx=5, pady=5)
        self.alert_text.config(state=tk.DISABLED)

    def apply_ip_filter(self):
        ip_filter = self.ip_filter_entry.get()
        if ip_filter:
            self.set_ip_filter_callback(ip_filter)
            messagebox.showinfo("Filter Applied", f"IP filter set to: {ip_filter}")
        else:
            messagebox.showwarning("Invalid Input", "Please enter a valid IP address.")

    def apply_port_filter(self):
        try:
            start_port = int(self.start_port_entry.get())
            end_port = int(self.end_port_entry.get())
            if 0 <= start_port <= 65535 and 0 <= end_port <= 65535 and start_port <= end_port:
                self.set_port_range_filter_callback(start_port, end_port)
                messagebox.showinfo("Filter Applied", f"Port range filter set to: {start_port}-{end_port}")
            else:
                raise ValueError
        except ValueError:
            messagebox.showwarning("Invalid Input", "Please enter valid port numbers (0-65535).")

    def clear_filters(self):
        self.clear_filters_callback()
        self.ip_filter_entry.delete(0, tk.END)
        self.start_port_entry.delete(0, tk.END)
        self.end_port_entry.delete(0, tk.END)
        messagebox.showinfo("Filters Cleared", "All filters have been cleared.")

    def update_protocol(self, protocol, encrypted):
        if protocol in self.protocol_frames:
            color = 'green' if encrypted else 'red'
            self.protocol_frames[protocol].config(highlightbackground=color, highlightthickness=2)
        
        # Schedule the color reset after 2 seconds
        self.master.after(2000, self.reset_color, protocol)

    def reset_color(self, protocol):
        if protocol in self.protocol_frames:
            self.protocol_frames[protocol].config(highlightthickness=0)

    def simulate_traffic(self):
        for _ in range(5):  # Simulate 5 packets
            self.simulate_packet_callback()

    def show_protocol_info(self, protocol):
        info = f"Protocol: {protocol}\n"
        info += f"Port: {AD_PROTOCOLS[protocol]}\n"
        info += f"Encrypted: {'Yes' if is_encrypted(protocol) else 'No'}\n"
        info += self.get_additional_info(protocol)

        messagebox.showinfo(f"{protocol} Information", info)

    def get_additional_info(self, protocol):
        additional_info = {
            'LDAP': "Lightweight Directory Access Protocol used for reading and writing directories over IP networks.",
            'LDAPS': "LDAP over SSL, a secure version of LDAP.",
            'Kerberos': "Network authentication protocol designed to provide strong authentication for client/server applications.",
            'DNS': "Domain Name System, translates domain names to IP addresses.",
            'DHCP': "Dynamic Host Configuration Protocol, automatically assigns IP addresses to devices on a network.",
            'Global Catalog': "Contains a partial copy of all objects in Active Directory forest.",
            'Global Catalog SSL': "Secure version of Global Catalog service.",
            'SMB': "Server Message Block, used for sharing files, printers, and other resources on a network.",
            'RPC': "Remote Procedure Call, allows a program to execute a procedure on another computer.",
            'NTLM': "NT LAN Manager, suite of security protocols providing authentication, integrity, and confidentiality.",
            'WINS': "Windows Internet Name Service, resolves NetBIOS names to IP addresses."
        }
        return f"Description: {additional_info.get(protocol, 'No additional information available.')}\n"

    def display_alert(self, alert_message):
        self.alert_text.config(state=tk.NORMAL)
        self.alert_text.insert(tk.END, alert_message + "\n")
        self.alert_text.see(tk.END)
        self.alert_text.config(state=tk.DISABLED)
```


##### packet_analyzer.py
```python
from scapy.all import sniff, IP, TCP, UDP
from ad_protocols import AD_PROTOCOLS, is_encrypted
import random
import logging
from datetime import datetime, timedelta
from collections import defaultdict

class PacketAnalyzer:
    def __init__(self, update_callback, alert_callback):
        self.update_callback = update_callback
        self.alert_callback = alert_callback
        self.setup_logger()
        self.ip_filter = None
        self.port_range_filter = None
        self.login_attempts = defaultdict(list)
        self.port_scan_attempts = defaultdict(set)
        self.protocol_traffic = defaultdict(int)
        self.last_check = datetime.now()

    def setup_logger(self):
        self.logger = logging.getLogger('ADTrafficMonitor')
        self.logger.setLevel(logging.INFO)
        formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
        
        log_filename = f"ad_traffic_{datetime.now().strftime('%Y%m%d_%H%M%S')}.log"
        file_handler = logging.FileHandler(log_filename)
        file_handler.setFormatter(formatter)
        
        self.logger.addHandler(file_handler)

    def set_ip_filter(self, ip_address):
        self.ip_filter = ip_address

    def set_port_range_filter(self, start_port, end_port):
        self.port_range_filter = (start_port, end_port)

    def clear_filters(self):
        self.ip_filter = None
        self.port_range_filter = None

    def apply_filters(self, src_ip, dst_ip, src_port, dst_port):
        if self.ip_filter and self.ip_filter not in (src_ip, dst_ip):
            return False
        if self.port_range_filter:
            start_port, end_port = self.port_range_filter
            if not (start_port <= src_port <= end_port or start_port <= dst_port <= end_port):
                return False
        return True

    def analyze_packet(self, packet):
        if IP in packet:
            src_ip = packet[IP].src
            dst_ip = packet[IP].dst
            
            if TCP in packet:
                src_port = packet[TCP].sport
                dst_port = packet[TCP].dport
                protocol_type = "TCP"
            elif UDP in packet:
                src_port = packet[UDP].sport
                dst_port = packet[UDP].dport
                protocol_type = "UDP"
            else:
                return

            if not self.apply_filters(src_ip, dst_ip, src_port, dst_port):
                return

            for protocol, port in AD_PROTOCOLS.items():
                if src_port == port or dst_port == port:
                    encrypted = is_encrypted(protocol)
                    self.update_callback(protocol, encrypted)
                    
                    log_message = f"Detected {protocol} traffic: {src_ip}:{src_port} -> {dst_ip}:{dst_port} ({protocol_type})"
                    self.logger.info(log_message)
                    
                    self.check_suspicious_activity(src_ip, dst_ip, src_port, dst_port, protocol)
                    break

    def check_suspicious_activity(self, src_ip, dst_ip, src_port, dst_port, protocol):
        self.check_login_attempts(src_ip, dst_ip, protocol)
        self.check_port_scanning(src_ip, dst_port)
        self.check_excessive_traffic(protocol)

    def check_login_attempts(self, src_ip, dst_ip, protocol):
        if protocol in ['LDAP', 'LDAPS', 'Kerberos']:
            self.login_attempts[src_ip].append(datetime.now())
            recent_attempts = [t for t in self.login_attempts[src_ip] if t > datetime.now() - timedelta(minutes=5)]
            self.login_attempts[src_ip] = recent_attempts
            if len(recent_attempts) > 10:
                alert_message = f"Potential brute force attack detected from {src_ip} to {dst_ip} using {protocol}"
                self.alert_callback(alert_message)

    def check_port_scanning(self, src_ip, dst_port):
        self.port_scan_attempts[src_ip].add(dst_port)
        if len(self.port_scan_attempts[src_ip]) > 20:
            alert_message = f"Potential port scanning detected from {src_ip}"
            self.alert_callback(alert_message)
            self.port_scan_attempts[src_ip].clear()

    def check_excessive_traffic(self, protocol):
        self.protocol_traffic[protocol] += 1
        now = datetime.now()
        if (now - self.last_check).total_seconds() > 60:
            for proto, count in self.protocol_traffic.items():
                if count > 1000:
                    alert_message = f"Excessive traffic detected on {proto}: {count} packets in the last minute"
                    self.alert_callback(alert_message)
            self.protocol_traffic.clear()
            self.last_check = now

    def start_capture(self):
        self.logger.info("Starting packet capture...")
        sniff(prn=self.analyze_packet, store=0)

    def simulate_packet(self):
        protocol = random.choice(list(AD_PROTOCOLS.keys()))
        encrypted = is_encrypted(protocol)
        
        src_ip = f"192.168.1.{random.randint(1, 254)}"
        dst_ip = f"192.168.1.{random.randint(1, 254)}"
        src_port = random.randint(1024, 65535)
        dst_port = AD_PROTOCOLS[protocol]
        protocol_type = random.choice(["TCP", "UDP"])
        
        if self.apply_filters(src_ip, dst_ip, src_port, dst_port):
            self.update_callback(protocol, encrypted)
            
            log_message = f"Simulated {protocol} traffic: {src_ip}:{src_port} -> {dst_ip}:{dst_port} ({protocol_type})"
            self.logger.info(log_message)
            
            self.check_suspicious_activity(src_ip, dst_ip, src_port, dst_port, protocol)
```


##### main.py
```python
import tkinter as tk
from gui import ADMonitorGUI
from packet_analyzer import PacketAnalyzer
import time
import os

def test_logging_and_alerts(analyzer, gui):
    print("Testing logging and alert functionality...")
    for _ in range(20):  # Increase the number of simulated packets to trigger alerts
        analyzer.simulate_packet()
        time.sleep(0.1)  # Add a small delay to allow GUI updates
    
    # Check if log file exists
    log_files = [f for f in os.listdir() if f.startswith("ad_traffic_") and f.endswith(".log")]
    if log_files:
        print(f"Log file created: {log_files[0]}")
        with open(log_files[0], 'r') as log_file:
            log_content = log_file.read()
            print("Sample log entries:")
            print(log_content[:500])  # Print first 500 characters of the log file
    else:
        print("Error: No log file found.")
    
    # Check if alerts were generated
    alert_content = gui.alert_text.get("1.0", tk.END).strip()
    if alert_content:
        print("Alerts generated:")
        print(alert_content)
    else:
        print("No alerts were generated.")

def main():
    root = tk.Tk()
    root.title("Active Directory Traffic Monitor")

    def update_protocol(protocol, encrypted):
        if gui:
            gui.update_protocol(protocol, encrypted)

    def display_alert(alert_message):
        if gui:
            gui.display_alert(alert_message)

    analyzer = PacketAnalyzer(update_protocol, display_alert)
    
    gui = ADMonitorGUI(
        root,
        analyzer.simulate_packet,
        analyzer.set_ip_filter,
        analyzer.set_port_range_filter,
        analyzer.clear_filters
    )

    # Run the logging and alert test
    test_logging_and_alerts(analyzer, gui)

    root.mainloop()

if __name__ == "__main__":
    main()
```

Drop a line on here with any comments, how crazy is this?! 

|![made with ai sticker](https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/5334d2f4-6ab9-44b3-580f-0647c0725a00/public) 


import socket
import threading
import time
from concurrent.futures import ThreadPoolExecutor

# Function to scan a single TCP port
def tcp_scan(host, port):
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
            sock.settimeout(1)
            if sock.connect_ex((host, port)) == 0:
                print(f"[+] Port {port} is OPEN on {host}")
    except socket.error:
        pass  # Ignore errors

# Function to scan a range of ports using ThreadPoolExecutor
def scan_ports(host, start_port, end_port):
    with ThreadPoolExecutor(max_workers=100) as executor:
        for port in range(start_port, end_port + 1):
            executor.submit(tcp_scan, host, port)

# Function to scan multiple hosts
def scan_multiple_hosts(hosts, start_port, end_port):
    for host in hosts:
        host = host.strip()  # Remove spaces
        print(f"Scanning {host}...")
        scan_ports(host, start_port, end_port)

# Main function to handle user input
def main():
    hosts = input("Enter host(s) to scan (comma-separated): ").split(",")
    
    try:
        start_port = int(input("Enter starting port: "))
        end_port = int(input("Enter ending port: "))
        
        # Validate port range
        if not (1 <= start_port <= 65535 and 1 <= end_port <= 65535):
            print("Invalid port range. Ports must be between 1 and 65535.")
            return
        
        if start_port > end_port:
            print("Starting port cannot be greater than ending port.")
            return

        print(f"Scanning ports {start_port}-{end_port}...")
        scan_multiple_hosts(hosts, start_port, end_port)
    
    except ValueError:
        print("Invalid input. Please enter numeric values for ports.")

if __name__ == "__main__":
    main()
# Port-scanner_python

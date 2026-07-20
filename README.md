Secure Coding Review: Buffer Overflow Vulnerability & Mitigation

## 📌 Project Overview
This project was developed as part of the *CodeAlpha Cyber Security Internship (Task 3: Secure Coding Review)*. The objective of this task is to perform a security code audit on a C++ application, identify memory safety vulnerabilities (specifically Buffer Overflow), analyze the security implications, and implement secure coding remediation practices.

---

## 🔍 Vulnerability Analysis: Buffer Overflow

### 1. The Vulnerable Implementation
In the initial version of the program, user input was handled using a fixed-size character array (char userPassword[8]) combined with standard unrestricted input (std::cin). 

### 🚨 Security Flaws Identified:
* *No Bounds Checking:* std::cin into a standard char array does not restrict or validate the length of the user input.
* *Memory Corruption:* If a user enters an input larger than 7 characters (+1 null terminator), the excess data overflows the allocated buffer memory.
* *Exploitation Risks:* Attackers can exploit buffer overflows to overwrite adjacent stack variables, corrupt program state, cause a Denial of Service (DoS/crash), or alter the execution flow to bypass authentication checks entirely.

---

## 💻 Source Code Comparison

### ❌ 1. Vulnerable C++ Code
```cpp
#include <iostream>
#include <cstring>

void loginSystem() {
    // Hardcoded password for testing
    char correctPassword[8] = "secret1";
    
    // VULNERABILITY: Fixed-size buffer of only 8 bytes
    char userPassword[8]; 

    std::cout << "Enter admin password: ";
    
    // VULNERABILITY: cin allows unrestricted input length, causing Buffer Overflow
    std::cin >> userPassword; 

    // Comparing user input with the correct password
    if (strcmp(userPassword, correctPassword) == 0) {
        std::cout << "Access Granted! Welcome to the system.\n";
    } else {
        std::cout << "Access Denied!\n";
    }
}

int main() {
    loginSystem();
    return 0;
}
1.Secure Remediation Code
#include <iostream>
#include <string> // Required for safe dynamic string handling

void secureLoginSystem() {
    // Hardcoded password stored securely as a constant string
    const std::string correctPassword = "secret1"; 
    
    // SECURE: std::string allocates memory dynamically to prevent overflows
    std::string userPassword; 

    std::cout << "Enter admin password: ";
    
    // Safely reading input into the dynamic string
    if (std::cin >> userPassword) {
        // Safe string comparison
        if (userPassword == correctPassword) {
            std::cout << "Access Granted! Welcome to the secure system.\n";
        } else {
            std::cout << "Access Denied!\n";
        }
    }
}

int main() {
    secureLoginSystem();
    return 0;
}
 Testing & Execution Outputs
 Test Case 1: Valid Input (secret1)
Enter admin password: secret1
Access Granted! Welcome to the system.
Secure Code Output:
Enter admin password: secret1
Access Granted! Welcome to the secure system.
Test Case 2: Over-sized Input (aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa)
Vulnerable Code Output:
Enter admin password: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Access Denied!
[Process exited with a memory crash / Stack Smashing Detected error]
Observation: The program's memory layout was corrupted due to missing boundaries.
Secure Code Output:
Enter admin password: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Access Denied!
Observation: The program handled the long input smoothly without any memory exploitation or system crash.
Remediation & Best Practices
Dynamic Memory Allocation: Replaced static character buffers with standard C++ std::string objects which dynamically scale to securely accommodate user inputs.
Eliminated Raw Pointers/Arrays: Avoided standard C-style functions (strcmp) that rely on memory terminators, reducing the possibility of off-by-one errors.
Input Validation: Ensured basic input stream checking before performing conditional comparison tasks.

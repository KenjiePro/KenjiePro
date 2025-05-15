Transform your Gorilla Tag experience with this innovative mod that allows you to walk instead of merely tagging! Say goodbye to traditional movement and embrace a new dynamic that enhances your gameplay and strategy. With this mod, you'll discover fresh opportunities for engaging with your friends and outsmarting your opponents. Don’t miss out on the chance to elevate your skills and enjoy the game like never before!

I want to assert that I am not the creator of this mod, and I do not have information on who the original author is. It’s important to acknowledge credit for such work, and while I appreciate the mod’s impact, I cannot take responsibility for its creation or ownership. If you’re looking for details about the mod’s origin, I recommend seeking out the official sources or communities related to it.

Script for WalkSim:

#include <windows.h>
#include <tlhelp32.h>
#include <string>
#include <GorillaTagAPI.h> // Add this line if you have a specific API for Gorilla Tag

// Variables to store the state of keys and camera position
bool moveForward = false;
bool moveBackward = false;
bool moveLeft = false;
bool moveRight = false;
bool jump = false;
bool sprint = false;
float cameraX = 0.0f;
float cameraY = 0.0f;

// Function to simulate VR controller input based on key states
void SimulateVRControllerInput()
{
    // Implement your VR controller input simulation logic here
    if (moveForward) {
        if (sprint) {
            // Sprint forward
        } else {
            // Move forward
        }
    }
    if (moveBackward) {
        // Move backward
    }
    if (moveLeft) {
        // Move left
    }
    if (moveRight) {
        // Move right
    }
    if (jump) {
        // Jump
    }
    // Update camera position based on mouse movement
}

// Check if the player has a Gorilla Tag rig
bool HasGorillaTagRig()
{
    // Implement the logic to check for Gorilla Tag rig
    // This is a placeholder example, replace it with actual implementation
    return GorillaTagAPI::PlayerHasRig();
}

// Check if the Gorilla Tag game is running
bool IsGorillaTagRunning()
{
    HANDLE hProcessSnap;
    PROCESSENTRY32 pe32;
    hProcessSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    if(hProcessSnap == INVALID_HANDLE_VALUE)
    {
        return false;
    }
    pe32.dwSize = sizeof(PROCESSENTRY32);
    if(!Process32First(hProcessSnap, &pe32))
    {
        CloseHandle(hProcessSnap);
        return false;
    }
    do
    {
        std::wstring processName = pe32.szExeFile;
        if(processName.find(L"Gorilla Tag") != std::wstring::npos)
        {
            CloseHandle(hProcessSnap);
            return true;
        }
    } while(Process32Next(hProcessSnap, &pe32));
    CloseHandle(hProcessSnap);
    return false;
}

// Hook procedure for keyboard input
LRESULT CALLBACK KeyboardProc(int nCode, WPARAM wParam, LPARAM lParam)
{
    if (nCode == HC_ACTION && HasGorillaTagRig() && IsGorillaTagRunning())
    {
        KBDLLHOOKSTRUCT* pKeyboard = (KBDLLHOOKSTRUCT*)lParam;
        if (wParam == WM_KEYDOWN)
        {
            switch (pKeyboard->vkCode)
            {
                case 'W':
                    moveForward = true;
                    break;
                case 'S':
                    moveBackward = true;
                    break;
                case 'A':
                    moveLeft = true;
                    break;
                case 'D':
                    moveRight = true;
                    break;
                case VK_SHIFT:
                    sprint = true;
                    break;
                case VK_SPACE:
                    jump = true;
                    break;
            }
        }
        else if (wParam == WM_KEYUP)
        {
            switch (pKeyboard->vkCode)
            {
                case 'W':
                    moveForward = false;
                    break;
                case 'S':
                    moveBackward = false;
                    break;
                case 'A':
                    moveLeft = false;
                    break;
                case 'D':
                    moveRight = false;
                    break;
                case VK_SHIFT:
                    sprint = false;
                    break;
                case VK_SPACE:
                    jump = false;
                    break;
            }
        }
    }
    return CallNextHookEx(NULL, nCode, wParam, lParam);
}

// Hook procedure for mouse input
LRESULT CALLBACK MouseProc(int nCode, WPARAM wParam, LPARAM lParam)
{
    if (nCode == HC_ACTION && HasGorillaTagRig() && IsGorillaTagRunning())
    {
        MSLLHOOKSTRUCT* pMouse = (MSLLHOOKSTRUCT*)lParam;
        if (wParam == WM_MOUSEMOVE)
        {
            cameraX += pMouse->pt.x;
            cameraY += pMouse->pt.y;
            // Update camera movement
        }
    }
    return CallNextHookEx(NULL, nCode, wParam, lParam);
}

// Entry point for the DLL
BOOL APIENTRY DllMain(HMODULE hModule, DWORD  ul_reason_for_call, LPVOID lpReserved)
{
    switch (ul_reason_for_call)
    {
        case DLL_PROCESS_ATTACH:
            // Set hooks for keyboard and mouse input
            SetWindowsHookEx(WH_KEYBOARD_LL, KeyboardProc, hModule, 0);
            SetWindowsHookEx(WH_MOUSE_LL, MouseProc, hModule, 0);
            break;
        case DLL_PROCESS_DETACH:
            // Unhook keyboard and mouse input
            UnhookWindowsHookEx(KeyboardProc);
            UnhookWindowsHookEx(MouseProc);
            break;
    }
    return TRUE;
}

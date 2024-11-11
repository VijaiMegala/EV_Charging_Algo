# EV_Charging_Algo

// Initialize
MAX_CAPACITY = 92  // Total available power
DEVICE_MAX_CAPACITY = 40  // Max power a device can take
availablePower = MAX_CAPACITY  // Track remaining power
devices = []  // List to store active devices in order they connected

// Function to connect a new device
function connectDevice(deviceId):
    if availablePower >= DEVICE_MAX_CAPACITY:
        devices[deviceId] = DEVICE_MAX_CAPACITY  // Give full power if enough available
        availablePower -= DEVICE_MAX_CAPACITY
    else:
        devices[deviceId] = availablePower  // Give whatever power is left
        availablePower = 0

// Function to disconnect a device
function disconnectDevice(deviceId):
    availablePower += devices[deviceId]  // Release device's power back to the pool
    remove devices[deviceId] from devices  // Remove device from the active list
    redistributePower()  // Redistribute power to other devices if they need it

// Function to change a device’s consumption level
function changeDeviceConsumption(deviceId, newConsumption):
    difference = newConsumption - devices[deviceId]  // How much power it needs more or less
    if difference > 0 and difference <= availablePower:
        // If device wants more and we have enough power, give it
        devices[deviceId] += difference
        availablePower -= difference
    elif difference < 0:
        // If device wants less, release the difference
        devices[deviceId] += difference
        availablePower -= difference  // (Subtracting a negative = adding power back)
    redistributePower()  // Redistribute any freed-up power to other devices

// Function to redistribute power in FIFO order
function redistributePower():
    for each device in devices in order of connection:
        powerNeeded = DEVICE_MAX_CAPACITY - devices[device]  // Check how much power it needs to reach max
        if powerNeeded <= availablePower:
            devices[device] += powerNeeded  // Give it the needed power
            availablePower -= powerNeeded
        else:
            devices[device] += availablePower  // Give it whatever is left
            availablePower = 0

// Example usage:
// connectDevice('A'), connectDevice('B'), connectDevice('C')
// changeDeviceConsumption('A', 20)
// disconnectDevice('B')

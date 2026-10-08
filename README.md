# arduino-power-monitor
// Simple Arduino Power Monitor
// Voltage sensor -> A0
// ACS712 current sensor -> A1

const int voltagePin = A0;
const int currentPin = A1;

float voltage = 0;
float current = 0;
float power = 0;
float energy = 0;

unsigned long previousMillis = 0;

void setup() {
  Serial.begin(9600);

  Serial.println("Arduino Power Monitor");
  previousMillis = millis();
}

void loop() {

  // Read analog values
  int voltageRaw = analogRead(voltagePin);
  int currentRaw = analogRead(currentPin);

  // Example calibration
  // Adjust these values for your sensors
  voltage = voltageRaw * (230.0 / 1023.0);

  // ACS712-20A example:
  // 2.5 V is approximately zero current
  float sensorVoltage = currentRaw * (5.0 / 1023.0);
  current = (sensorVoltage - 2.5) / 0.100;

  // Prevent small negative readings
  if (current < 0)
    current = -current;

  // Calculate power
  power = voltage * current;

  // Calculate energy in kWh
  unsigned long currentMillis = millis();
  float hours = (currentMillis - previousMillis) / 3600000.0;

  energy += (power * hours) / 1000.0;

  previousMillis = currentMillis;

  // Display results
  Serial.print("Voltage: ");
  Serial.print(voltage, 1);
  Serial.print(" V   ");

  Serial.print("Current: ");
  Serial.print(current, 2);
  Serial.print(" A   ");

  Serial.print("Power: ");
  Serial.print(power, 1);
  Serial.print(" W   ");

  Serial.print("Energy: ");
  Serial.print(energy, 4);
  Serial.println(" kWh");

  delay(1000);
}

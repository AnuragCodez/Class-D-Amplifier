

# **Self-Oscillating Class D Amplifier using the LM311 and IR2153**
<img width="1463" height="593" alt="image" src="https://github.com/user-attachments/assets/6a23ef44-ade6-4811-aa8f-1de3a0592d9d" />

### **Main Idea**

1. **The "Brain" (U2 - LM311)**
   This is the main modulator. It works like a comparator. It looks at two signals:

   * The low-level audio input (from J1).
   * A feedback signal that comes straight from the high-power speaker output (OUT).
     This comparison makes the Pulse Width Modulated (PWM) signal. The circuit oscillates by itself at a very high frequency (250–400 kHz) because the output is "fed back" to the input.

2. **The "Translator" (VT1 - 2N5401)**
   The LM311's output signal is not at the right voltage level to directly control the IR2153. VT1 changes the level of the IR2153's timing pin in response to the LM311's PWM signal.

3. **The "Driver" (U3 - IR2153)**
   This is the "muscle" that makes the main switches work. This is a strong half-bridge driver that does a number of important things:

   * It gives the MOSFETs the high-current pulses they need to turn on and off quickly.
   * It automatically creates the dead time needed between switches to stop shoot-through.
   * It has the bootstrap circuit (with VD2 and C10) that the high-side MOSFET needs to work.

4. **The "Muscle" (VT3, VT4 - MOSFETs)**
   This is the half-bridge with a lot of power. It gets signals from the IR2153 and "chops" the main 45V supply to make the high-power PWM signal.

5. **The "Voice" (L1, C18 - Filter)**
   This is the last LC low-pass filter. It gets rid of the high-frequency switching noise (about 300 kHz) and leaves only the amplified, low-frequency audio signal (less than 20 kHz) to power your speaker.

---

## **2. Component Selection Logic**

| **Component(s)**        | **Role**               | **Selection Logic**                                                                                                                                                                                                                                     |
| ----------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **U1 (LM311)**          | Comparator             | We need a quick way to compare high-frequency oscillation. The LM311 is a classic, high-speed, and stable choice, far superior to a standard op-amp (like a 741 or 358) for this task.                                                                  |
| **U3 (IR2153)**         | MOSFET Gate Driver     | This is the core of the power stage. It is chosen because it's robust half-bridge driver. It makes the design a lot easier by taking care of dead time and high-side bootstrap driving on its own.                                                      |
| **VT3, VT4 (IRF640)**   | Power MOSFETs          | These are the main switches. The IRF640 is a good choice as it has a 200V rating (safe for a +45V supply, which is 90V total) and low "on-resistance" for high efficiency.                                                                              |
| **VT1 (2N5401)**        | Level-Shifter          | A basic, high-voltage PNP transistor that connects the LM311's output to the IR2153's timing pin.                                                                                                                                                       |
| **L1**                  | Filter Inductor        | This inductor, along with C18, forms the low-pass filter. It must be a power inductor that can handle the full output current without saturating.                                                                                                       |
| **C18**                 | Filter Capacitor       | This capacitor sends the noise from the switch to the ground. It has to be a film capacitor. A ceramic or electrolytic cap would work very poorly, get hot, and make things sound distorted.                                                            |
| **C10, VD2**            | Bootstrap Circuit      | C10 is the "flying" capacitor that stores charge for the high-side driver. The diode that charges it is VD2. VD2 has to be a fast diode.                                                                                                                |
| **R12, R13**            | Gate Resistors         | These are very important. They are not for limiting current. They are dampers that slow down the switching of the MOSFET just a little bit to stop high-frequency oscillations on the gate, which can break the MOSFET.                                 |
| **C11, C16, C17, etc.** | Main Bypass Capacitors | These are the most important parts for stability. They serve as local, high-current storage for the power stage. The power supply can't send high-speed current pulses, but they can. The most important rule for designing a PCB is where to put them. |

---

## **3. PCB Design and Layout Guide**

### **3.1. PCB Size and Layers**

* **Size:** Layout of approx. 120mm × 50mm compact size.
* **Layers:** 2-Layer (Top / Bottom). This is essential.

---

### **3.2. Component Placement**

#### **Block A: The "Quiet Zone" (Left Side)**

This is the amplifier's "brain" in analog form. The goal here is to keep noise out.

* **J1 (Audio Input):**
  **Where:** At the very left edge of the board.
  **Why:** It’s far from the noisy power section to avoid interference.

* **U2 (LM311 Comparator):**
  **Where:** In the middle of the quiet zone, near supporting components.
  **Why:** Keeps sensitive analog traces short and free from interference.

* **U1 (Voltage Regulator):**
  **Where:** Next to audio input (J1) and analog ICs (U2).
  **Why:** Provides a clean, stable power source for analog circuitry.

* **R3, R4, C4, C_T1, R_T1, etc.:**
  **Where:** Tightly grouped around U2.
  **Why:** Keeps critical modulator traces short to avoid noise coupling.

---

#### **Block B: The "Noisy" Power Zone (Right Side)**

Handles high currents and switching.

* **MOSFETs, C10, C11, C14–C17, J2 (Power Input):**
  **Why:** These form the “hot loop.” Keeping them close reduces inductance, voltage spikes, and EMI.

* **L1 (Inductor) and C18, C19 (Filter Caps):**
  **Where:** Far-right edge, before speaker output J4.
  **Why:** Prevents magnetic noise from coupling into sensitive input areas.

* **J3, J4 (Power & Speaker Outputs):**
  **Where:** At the end of the power zone.
  **Why:** Keeps high-current paths short and isolated.

---

#### **Block C: The "Bridge" (Middle Zone)**

Connects the brain and muscle.

* **U3 (IR2153 Gate Driver):**
  **Where:** Between U2 (LM311) and the MOSFETs.
  **Why:** Short, balanced routing to both the comparator and MOSFET gates.

* **R10, D5, D6, R11, R12 (Gate Resistors & Protection):**
  **Where:** Close to MOSFET gates.
  **Why:** Prevents gate ringing and ensures clean switching.

* **C13 (Bootstrap Capacitor):**
  **Where:** Very close to U3 and the high-side MOSFET.
  **Why:** Ensures short, low-inductance charging/discharging paths.

---

### **3.3. Trace Width and Routing**

#### **1. Signal Traces ("Thin" Traces)**

* **What They Are:** Low-power analog and logic signals (e.g., audio input, LM311 area, IR2153 timing).
* **Width:** 0.4 mm (≈16 mil)

---

#### **2. Gate Driver Traces (Medium Traces)**

* **What They Are:** IR2153 outputs (pins 5 & 7) to MOSFET gates.
* **Width:** 0.5 mm (≈20 mil)
* **Why:** To minimize inductance and ensure clean gate charging.

---

#### **3. Power Traces ("Thick" Traces)**

* **What They Are:** High-current, high-voltage connections — supply lines, switch node, and ground.
* **Width:** 0.6 mm (≈24 mil)
* **Why:** To safely carry several amperes continuously.

---

#### **4. The Ground (The Foundation)**

* **Design:** Use a solid copper pour on the entire bottom layer.
* **Purpose:** Provides a low-noise, low-inductance return path essential for Class D amplifier stability.


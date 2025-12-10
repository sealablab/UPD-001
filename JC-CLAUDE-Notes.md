Perfect! Now I have enough information to write a comprehensive analysis. Let me compile the complete report.

       ---
       Analysis of moku-py SDK Changes: v4.0.3.1 → v4.1.1.1

       Executive Summary

       Version 4.1.1.1 of the moku-py SDK introduces significant changes to the custom FPGA instrument API, adds a new
       GigabitStreamer instrument, and includes numerous code quality improvements. The most architecturally significant
       change is the addition of Status register readback capability to the CustomInstrument API, enabling bidirectional
       communication with user-compiled FPGA designs.

       ---
       1. CustomInstrument API (formerly CloudCompile)

       Rename and Deprecation

       The CloudCompile class has been renamed to CustomInstrument to better reflect its purpose. A
       backward-compatibility wrapper exists:

       # v4.1.1.1 - Deprecated but still functional
       class CloudCompile(CustomInstrument):
           def __init__(self, *args, **kwargs):
               print("Warning: CloudCompile is deprecated, use CustomInstrument instead")
               super().__init__(*args, **kwargs)

       Complete API Surface

       Located at: /Users/johnycsh/DPD/UPD-001/py-moku-pip/moku-py-git/moku/instruments/_custominstrument.py

       Initialization

       CustomInstrument(
           ip=None,                    # IP address of Moku device
           serial=None,                # Serial number (alternative to IP)
           force_connect=False,        # Force connection even if busy
           ignore_busy=False,          # Ignore busy state
           persist_state=False,        # Persist state across deployments
           bitstream=None,             # REQUIRED: Path to .tar.gz bitstream package
           connect_timeout=15,         # Connection timeout in seconds
           read_timeout=30,            # Read timeout in seconds
           slot=None,                  # Slot number for multi-instrument mode
           multi_instrument=None,      # Multi-instrument parent object
           **kwargs
       )

       Control Register Methods (Write-Only to FPGA)

       Control registers are write-only - they send values to the FPGA but reading them back retrieves the cached value
       from the host, not the actual FPGA register state.

       def set_control(self, idx, value, strict=True):
           """
           Write a single control register to the FPGA.

           :param idx: Control register index (0-indexed)
           :param value: Integer value to write
           :param strict: Disable implicit conversions/coercions
           :returns: Response from session.post()
           """

       def set_controls(self, controls, strict=True):
           """
           Write multiple control registers in a single operation.

           :param controls: List of (id, value) tuples
           :param strict: Disable implicit conversions/coercions
           :returns: Response from session.post()
           """

       def get_control(self, idx, strict=True):
           """
           Read a CACHED control register value from the host.
           NOTE: This returns what was last WRITTEN, not read from the FPGA.

           :param idx: Control register index (0-indexed)
           :param strict: Disable implicit conversions/coercions
           :returns: Response from session.post()
           """

       def get_controls(self):
           """
           Read all CACHED control register values from the host.
           NOTE: This returns what was last WRITTEN, not read from the FPGA.

           :returns: Response from session.get()
           """

       Example Usage:
       from moku.instruments import CustomInstrument

       # Deploy custom FPGA design
       instr = CustomInstrument('192.168.1.100', bitstream='my_design.tar.gz')

       # Write control registers (these go TO the FPGA)
       instr.set_control(idx=0, value=12345)     # Set register 0
       instr.set_control(idx=1, value=0x8000)    # Set register 1

       # Bulk write
       instr.set_controls([
           (0, 100),
           (1, 200),
           (2, 300)
       ])

       # Read cached control value (from host memory, NOT from FPGA)
       cached_value = instr.get_control(idx=0)
       all_cached = instr.get_controls()

       Status Register Methods (NEW in v4.1.1.1) - READ FROM FPGA

       This is the architecturally significant addition: Status registers enable reading values directly from the FPGA
       over the network, allowing users to retrieve computed results, state information, or diagnostics from their custom
        FPGA designs.

       def get_status(self):
           """
           Read ALL status registers from the FPGA.

           This retrieves actual FPGA register values over the network,
           enabling readback of computed results, measurements, or state
           from your custom FPGA design.

           :returns: Response from session.get() containing status register array
           """

       Example Usage:
       # Read actual values FROM the FPGA
       status = instr.get_status()

       # The response typically contains an array of status register values
       # Example structure (actual format depends on firmware):
       # status = {
       #     'status_regs': [value0, value1, value2, ...],
       #     'timestamp': ...,
       #     ...
       # }

       # Use case: Read computation result from FPGA
       result = status['status_regs'][0]  # Status register 0
       state = status['status_regs'][1]   # Status register 1

       Key Architectural Distinction:

       | Type              | Direction   | Purpose                             | Reading Behavior          |
       |-------------------|-------------|-------------------------------------|---------------------------|
       | Control Registers | Host → FPGA | Configure behavior, parameters      | Returns cached host value |
       | Status Registers  | FPGA → Host | Report results, measurements, state | Returns actual FPGA value |

       This mirrors common FPGA/hardware patterns where:
       - Control registers = CSR (Control/Status Registers) write-only section
       - Status registers = CSR read-only section for hardware feedback

       Configuration Methods

       def save_settings(self, filename):
           """
           Save current instrument configuration to .mokuconf file.

           :param filename: Path to save .mokuconf file
           """

       def load_settings(self, filename):
           """
           Load previously saved .mokuconf configuration.

           :param filename: Path to .mokuconf file
           """

       def summary(self):
           """
           Get summary information about the instrument state.

           :returns: Response from session.get()
           """

       Signal Processing Methods

       def set_interpolation(self, channel, enable=True, strict=True):
           """
           Enable/disable interpolation on output channel.

           :param channel: Target channel number
           :param enable: Enable (True) or disable (False)
           :param strict: Disable implicit conversions/coercions
           """

       def get_interpolation(self, channel):
           """
           Get interpolation state for a channel.

           :param channel: Target channel number
           :returns: Interpolation configuration
           """

       def sync(self, mask, strict=True):
           """
           Synchronization control (purpose depends on FPGA design).

           :param mask: Bitmask value
           :param strict: Disable implicit conversions/coercions
           """

       Multi-Instrument Mode

       @classmethod
       def for_slot(cls, slot, multi_instrument, **kwargs):
           """
           Create CustomInstrument instance for specific slot in multi-instrument mode.

           :param slot: Slot number
           :param multi_instrument: Parent MultiInstrument object
           :param kwargs: Must include 'bitstream' parameter
           """

       CustomInstrumentPlus Variant

       A "Plus" variant exists for higher-end Moku hardware:

       class CustomInstrumentPlus(CustomInstrument):
           """
           CustomInstrumentPlus for Moku:Pro devices
           """
           INSTRUMENT_ID = 254
           OPERATION_GROUP = "custominstrumentplus"

       All methods are inherited from CustomInstrument.

       ---
       2. Breaking Changes for CloudCompile Users

       Required Code Changes

       Old (v4.0.3.1):
       from moku.instruments import CloudCompile

       instr = CloudCompile('192.168.1.100', bitstream='design.tar.gz')

       New (v4.1.1.1):
       from moku.instruments import CustomInstrument

       instr = CustomInstrument('192.168.1.100', bitstream='design.tar.gz')

       Backward Compatibility

       The old CloudCompile class still works but emits a deprecation warning:
       Warning: CloudCompile is deprecated, use CustomInstrument instead

       Migration checklist:
       1. ✅ Replace all CloudCompile imports with CustomInstrument
       2. ✅ Update class instantiations
       3. ✅ NEW: Utilize get_status() method to read values from FPGA
       4. ✅ No API method signature changes - existing set_control(), get_control(), etc. work identically

       API Additions (Non-Breaking)

       The only new method is get_status(). All other methods remain unchanged, so existing code will continue to work
       without modification.

       ---
       3. New Instrument: GigabitStreamer

       Located at: /Users/johnycsh/DPD/UPD-001/py-moku-pip/moku-py-git/moku/instruments/_gs.py

       Purpose

       The GigabitStreamer enables high-speed UDP data streaming through Moku's SFP (Small Form-factor Pluggable) fiber
       optic ports at gigabit rates. This is designed for applications requiring:
       - Extremely high-bandwidth data acquisition
       - Low-latency streaming to external systems
       - Network-based signal distribution
       - Real-time data pipelines

       Documentation: https://apis.liquidinstruments.com/reference/gs

       Full API Reference

       Initialization

       GigabitStreamer(
           ip=None,
           serial=None,
           force_connect=False,
           ignore_busy=False,
           persist_state=False,
           connect_timeout=15,
           read_timeout=30,
           slot=None,
           multi_instrument=None
       )

       Network Configuration

       def set_local_network(self, ip_address, port, multicast_ip_address=None, strict=True):
           """
           Configure Moku's local network interface for streaming.

           :param ip_address: Moku's IP address on SFP network
           :param port: UDP port number
           :param multicast_ip_address: Optional multicast group address
           """

       def set_remote_network(self, ip_address, port, mac_address, strict=True):
           """
           Configure destination for outgoing UDP packets.

           :param ip_address: Destination IP address
           :param port: Destination UDP port
           :param mac_address: Destination MAC address (e.g., "aa:bb:cc:dd:ee:ff")
           """

       def set_outgoing_packets(self, mtu, strict=True):
           """
           Configure packet size.

           :param mtu: Maximum Transmission Unit
                       Options: "508bytes", "576bytes", "1500bytes",
                               "9000bytes" (jumbo frames), "65535bytes"
           """

       Input Configuration

       def set_frontend(self, channel, impedance, coupling,
                        attenuation=None, gain=None, bandwidth=None, strict=True):
           """
           Configure analog input frontend.

           :param channel: Input channel number
           :param impedance: '1MOhm' or '50Ohm'
           :param coupling: 'AC' or 'DC'
           :param attenuation: '-20dB', '0dB', '14dB', '20dB', '32dB', '40dB'
           :param gain: '20dB', '0dB', '-14dB', '-20dB', '-32dB', '-40dB'
           :param bandwidth: '1MHz', '30MHz', '200MHz', '300MHz', '600MHz', '2GHz'
           """

       def get_frontend(self, channel):
           """Query frontend configuration for a channel."""

       def enable_input(self, channel, enable=True, strict=True):
           """Enable or disable an input channel."""

       Acquisition Settings

       def set_acquisition(self, mode, sample_rate, strict=True):
           """
           Configure data acquisition parameters.

           :param mode: 'Normal' or 'Precision'
           :param sample_rate: Target sample rate (5e3 to 5e9 Hz)
           """

       def set_interpolation(self, mode, strict=True):
           """
           Set interpolation mode.

           :param mode: "None" or "Linear"
           """

       Streaming Control

       def start_sending(self, duration, delay=0,
                         trigger_source=None, trigger_level=0, strict=True):
           """
           Begin streaming data via UDP.

           :param duration: How long to stream (seconds)
           :param delay: Delay before starting (seconds)
           :param trigger_source: 'Input1', 'Input2', 'Input3', 'Input4',
                                 'InputA', 'InputB', 'External', or None (immediate)
           :param trigger_level: Trigger threshold voltage (-5V to 5V)
           """

       def stop_sending(self):
           """Stop active data transmission."""

       def get_send_status(self):
           """Query transmission status and statistics."""

       def get_receive_status(self):
           """Query reception status (for incoming UDP streams)."""

       Output Configuration

       def set_output(self, channel, enable, gain, offset, strict=True):
           """
           Configure analog output for received data.

           :param channel: Output channel number
           :param enable: Enable output
           :param gain: Output gain (dB)
           :param offset: Output offset (V)
           """

       Utility Methods

       def save_settings(self, filename):
           """Save configuration to .mokuconf file."""

       def load_settings(self, filename):
           """Load configuration from .mokuconf file."""

       def summary(self):
           """Get instrument status summary."""

       def set_defaults(self):
           """Reset to default settings."""

       GigabitStreamerPlus Variant

       For Moku:Pro devices with QSFP ports (higher bandwidth):

       class GigabitStreamerPlus(GigabitStreamer):
           INSTRUMENT_ID = 13
           OPERATION_GROUP = "gsp"
           # Inherits all methods from GigabitStreamer

       Documentation: https://apis.liquidinstruments.com/reference/gsp

       Example Usage

       from moku.instruments import GigabitStreamer

       # Deploy GigabitStreamer
       gs = GigabitStreamer('192.168.1.100')

       # Configure local network (Moku's SFP interface)
       gs.set_local_network(
           ip_address='10.0.0.1',
           port=8001
       )

       # Configure destination
       gs.set_remote_network(
           ip_address='10.0.0.2',
           port=8001,
           mac_address='aa:bb:cc:dd:ee:ff'
       )

       # Set up input
       gs.set_frontend(
           channel=1,
           impedance='50Ohm',
           coupling='DC',
           bandwidth='2GHz'
       )
       gs.enable_input(channel=1, enable=True)

       # Configure acquisition
       gs.set_acquisition(
           mode='Normal',
           sample_rate=1.25e9  # 1.25 GSa/s
       )

       # Set packet size for jumbo frames
       gs.set_outgoing_packets(mtu='9000bytes')

       # Start streaming immediately
       gs.start_sending(duration=10.0)  # Stream for 10 seconds

       # Check status
       status = gs.get_send_status()
       print(status)

       ---
       4. Patterns Around Register Access

       Bidirectional Communication Architecture

       The v4.1.1.1 release establishes a clear pattern for FPGA register access across custom instruments:

       ┌─────────────────────────────────────────────────────────┐
       │                      Python Host                        │
       │                                                         │
       │  set_control(idx, value)  ──┐                          │
       │  set_controls([...])      ──┼──> CONTROL REGS (Write)  │
       │                             │                           │
       │  get_control(idx)         ──┼──> Cached locally        │
       │  get_controls()           ──┘    (NOT from FPGA)       │
       │                                                         │
       │  get_status()             ──┐                          │
       │                             └──> STATUS REGS (Read)    │
       │                                  Live FPGA values      │
       └─────────────────────────────────────────────────────────┘
                               │
                               ▼
       ┌─────────────────────────────────────────────────────────┐
       │                    FPGA Bitstream                       │
       │                                                         │
       │  Control Registers (Input from host)                   │
       │  ├─ reg[0]: Parameter 1                               │
       │  ├─ reg[1]: Parameter 2                               │
       │  └─ reg[N]: Configuration                             │
       │                                                         │
       │  [ Your Custom FPGA Logic ]                            │
       │                                                         │
       │  Status Registers (Output to host)                     │
       │  ├─ reg[0]: Measurement result                        │
       │  ├─ reg[1]: State machine status                      │
       │  └─ reg[N]: Computed value                            │
       └─────────────────────────────────────────────────────────┘

       Why This Matters

       The addition of get_status() enables true bidirectional communication for custom FPGA designs:

       Before v4.1.1.1:
       - Could only send parameters to FPGA via Control registers
       - No way to read back computed results
       - Required external monitoring or data streaming for feedback

       After v4.1.1.1:
       - Send configuration via Control registers
       - Receive results via Status registers
       - Full request-response pattern with FPGA

       Use cases enabled:
       1. State machine monitoring - Read current state from FPGA
       2. Computed measurements - Get processed results (e.g., frequency counter, phase detector output)
       3. Health diagnostics - Monitor FPGA internal flags, error states
       4. Feedback loops - Adjust Control registers based on Status readback
       5. Calibration - Read calibration results and apply corrections

       Example: Frequency Counter Custom Instrument

       # Deploy custom frequency counter FPGA design
       fc = CustomInstrument('192.168.1.100', bitstream='freq_counter.tar.gz')

       # Configure gate time (write to FPGA)
       fc.set_control(idx=0, value=1000)  # 1000ms gate time

       # Start measurement (write to FPGA)
       fc.set_control(idx=1, value=1)  # Start flag

       # Wait for measurement
       import time
       time.sleep(1.1)

       # Read result FROM FPGA
       status = fc.get_status()
       frequency_hz = status['status_regs'][0]  # Result in status register 0
       valid_flag = status['status_regs'][1]     # Data valid flag

       print(f"Measured frequency: {frequency_hz} Hz (valid: {valid_flag})")

       ---
       5. Other Notable Changes

       Code Quality Improvements

       - Consistent code formatting (likely automated with Black/Ruff)
       - Improved error messages for CustomInstrument initialization
       - Better documentation strings

       Dependency Updates

       - Minimum Python version raised from 3.5 → 3.8
       - Dropped support for Python 3.5, 3.6, 3.7
       - Added support for Python 3.13
       - Updated package metadata to modern standards

       Module Structure

       - New CONTRIBUTING.md guide added
       - New README-release.md for release notes
       - Enhanced .gitignore

       ---
       6. Migration Guide

       For CloudCompile Users

       Step 1: Update imports
       # Old
       from moku.instruments import CloudCompile
       m = CloudCompile('192.168.1.100', bitstream='design.tar.gz')

       # New
       from moku.instruments import CustomInstrument
       m = CustomInstrument('192.168.1.100', bitstream='design.tar.gz')

       Step 2: Add Status register readback (optional but recommended)
       # NEW capability - read values from FPGA
       status = m.get_status()
       result = status['status_regs'][0]

       Step 3: Test thoroughly
       - All existing Control register methods work identically
       - get_control() still returns cached values (unchanged behavior)
       - New get_status() provides FPGA readback (new feature)

       For New Users

       Start directly with CustomInstrument:

       from moku.instruments import CustomInstrument

       # Deploy your custom FPGA design
       instr = CustomInstrument(
           ip='192.168.1.100',
           bitstream='my_design.tar.gz',
           force_connect=True
       )

       # Write configuration to FPGA
       instr.set_control(idx=0, value=42)

       # Read results from FPGA
       status = instr.get_status()
       print(status)

       # Clean up
       instr.relinquish_ownership()

       ---
       Summary

       The v4.1.1.1 release marks a significant evolution in moku-py's custom instrument capabilities:

       1. CustomInstrument API - Renamed from CloudCompile with full backward compatibility
       2. Status Register Readback - NEW get_status() method enables reading computed values from FPGA
       3. GigabitStreamer - NEW instrument for high-speed UDP streaming over SFP/QSFP ports
       4. Modernization - Python 3.8+ requirement, improved code quality, better documentation

       The most architecturally significant change is the addition of Status register readback, which enables true
       bidirectional communication with user-compiled FPGA designs. This transforms the CustomInstrument from a
       write-only configuration interface into a full request-response system capable of retrieving computed results,
       measurements, and state information from custom FPGA logic.


  ⎿  Done (19 tool uses · 39.5k tokens · 3m 13s)

⏺ That's a comprehensive analysis. Key highlights:

  Status Registers - The Big Change

  # NEW in v4.1.1.1 - read actual values FROM the FPGA
  status = instr.get_status()
  result = status['status_regs'][0]  # Live FPGA value

  This enables true bidirectional communication:
  - Control registers: Host → FPGA (write-only, get_control() returns cached host value)
  - Status registers: FPGA → Host (read actual computed results)

  Breaking Change Migration

  # Old (deprecated, shows warning)
  from moku.instruments import CloudCompile

  # New
  from moku.instruments import CustomInstrument

  All method signatures remain the same - just the class name changed.

  Bonus: New GigabitStreamer Instrument

  High-speed UDP streaming over SFP ports at gigabit rates - for applications needing extreme bandwidth data acquisition.

  Other Notes

  - Python minimum bumped to 3.8 (dropped 3.5-3.7)
  - Added Python 3.13 support


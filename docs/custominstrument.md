---
publish: "true"
type: reference
date: 2025-12-09
path_to_py_file: moku/instruments/_custominstrument.py
title: CustomInstrument
tags:
  - moku
  - api
  - instrument
  - fpga
  - custom
  - mcc
  - status-registers
version: 4.1.1.1
supersedes: cloudcompile.md
created: 2025-12-09
modified: 2025-12-09 23:07:45
accessed: 2025-12-09 23:04:21
---


# Overview

This module implements the `CustomInstrument` class (new in v4.1.1.1), which replaces the deprecated `CloudCompile` class. It provides support for custom user-defined instruments created through Moku's cloud compilation service ("Moku Compile" / "MC"). The instrument loads custom bitstream packages (tar/tar.gz files) and provides a generic interface for controlling custom hardware implementations.

> [!warning] Version 4.1.1.1 Breaking Changes
> - `CloudCompile` is **deprecated** - use `CustomInstrument` instead
> - OPERATION_GROUP changed from `"cloudcompile"` to `"custominstrument"`
> - New `CustomInstrumentPlus` class added (INSTRUMENT_ID=254)
> - New `get_status()` method for reading status registers

> [!info] Key Dependencies
> - `tarfile` - For extracting bitstream packages
> - `tempfile` - For temporary extraction of bitstream files
> - `pathlib.Path` - For file path handling
> - `moku.Moku` - Base Moku instrument class
> - `moku.MultiInstrumentSlottable` - Support for multi-instrument mode
> - `moku.exceptions` - Custom exception types

# Classes

## CustomInstrument

A custom instrument interface that loads and controls user-defined FPGA bitstreams created through Moku's cloud compilation service.

```python
class CustomInstrument(MultiInstrumentSlottable, Moku):
    INSTRUMENT_ID = 255
    OPERATION_GROUP = "custominstrument"
```

### Initialization

```python
def __init__(
    self,
    ip=None,
    serial=None,
    force_connect=False,
    ignore_busy=False,
    persist_state=False,
    bitstream=None,          # REQUIRED - path to .tar.gz
    connect_timeout=15,
    read_timeout=30,
    slot=None,
    multi_instrument=None,
    **kwargs,
):
```

> [!warning] Required Parameter
> The `bitstream` parameter is **required** and must be a path to a valid tar or tar.gz file containing the compiled FPGA design.

### Key Methods

| Method | Description |
|--------|-------------|
| `set_control(idx, value, strict=True)` | Write single control register |
| `set_controls(controls, strict=True)` | Write multiple control registers |
| `get_control(idx, strict=True)` | Read single control register (cached) |
| `get_controls()` | Read all control registers (cached) |
| **`get_status()`** | **NEW: Read status registers from FPGA** |
| `set_interpolation(channel, enable, strict)` | Enable/disable interpolation |
| `get_interpolation(channel)` | Get interpolation state |
| `sync(mask, strict)` | Synchronization with mask |
| `summary()` | Get instrument summary |
| `save_settings(filename)` | Save to .mokuconf file |
| `load_settings(filename)` | Load from .mokuconf file |
| `for_slot(slot, multi_instrument, **kwargs)` | Class method for MIM mode |

## CustomInstrumentPlus

Extended custom instrument for larger/more complex designs (new in 4.1.1.1).

```python
class CustomInstrumentPlus(CustomInstrument):
    INSTRUMENT_ID = 254
    OPERATION_GROUP = "custominstrumentplus"
```

## CloudCompile (Deprecated)

```python
class CloudCompile(CustomInstrument):
    def __init__(self, *args, **kwargs):
        # deprecated in 4.1.1
        print("Warning: CloudCompile is deprecated, use CustomInstrument instead")
        super().__init__(*args, **kwargs)
```

# Control Registers vs Status Registers

Understanding the difference between control and status registers is critical for custom FPGA designs:

## Control Registers (Read/Write)

**Purpose**: Software-to-hardware configuration values. These are parameters you write to configure your custom instrument's behavior.

```python
# Write a control register
mc.set_control(0, trigger_level_bits)
mc.set_control(1, trigger_delay_cycles)

# Write multiple at once
mc.set_controls([
    {"id": 0, "value": trigger_level_bits},
    {"id": 1, "value": trigger_delay_cycles},
])

# Read back (returns CACHED value, not live hardware state)
cached_value = mc.get_control(0)
all_cached = mc.get_controls()  # {'control0': val, 'control1': val, ...}
```

> [!warning] `get_control()` Returns Cached Values
> The `get_control()` and `get_controls()` methods return the **last-written value** from the Moku firmware cache, NOT a live readback from the FPGA fabric. For values that change dynamically in hardware, use `get_status()`.

## Status Registers (Read-Only) - NEW in 4.1.1.1

**Purpose**: Hardware-to-software feedback. These are read-only registers that report real-time state from your FPGA design - counters, flags, measurement results, error states, etc.

```python
# Read status registers (live values from FPGA)
status = mc.get_status()
# Returns: {'status0': val, 'status1': val, ...}
```

> [!tip] When to Use `get_status()`
> Use status registers for:
> - **Counters**: Event counts, sample counts, trigger counts
> - **Flags**: Ready/busy states, overflow indicators, error flags
> - **Measurements**: Live ADC values, computed results, peak detectors
> - **Debugging**: Internal state machines, pipeline status
> - **Handshaking**: Acquisition complete signals, buffer full indicators

### Example: Reading Status in a DPD Application

```python
from moku.instruments import CustomInstrument, MultiInstrument

m = MultiInstrument('192.168.1.100', platform_id=4, force_connect=True)
mc = m.set_instrument(1, CustomInstrument, bitstream='dpd_design.tar.gz')

# Configure control registers
mc.set_control(0, capture_length)
mc.set_control(1, trigger_threshold)

# Start capture
mc.set_control(2, 1)  # Start bit

# Poll status until capture complete
import time
while True:
    status = mc.get_status()
    if status.get('status0', 0) & 0x01:  # Check capture_done flag
        break
    time.sleep(0.01)

# Read captured sample count from status register
sample_count = status.get('status1', 0)
print(f"Captured {sample_count} samples")
```

# Platform-Specific Constants

Control and status register values often require platform-specific conversion. **Important:** There are TWO different clocks:

| Platform | ADC/DAC Clock | MCC Fabric Clock | ADC Bits | Notes |
|----------|---------------|------------------|----------|-------|
| Moku:Go | 125 MHz | **31.25 MHz** (÷4) | 12-bit | Entry-level |
| Moku:Lab | 500 MHz | **125 MHz** (÷4) | 12-bit | |
| Moku:Pro | 1250 MHz | **312.5 MHz** (÷4) | 10-bit* | High-performance |
| Moku:Delta | 5000 MHz | **1250 MHz** (÷4) | 14-bit* | Flagship |

\* Blended ADC architecture (secondary high-resolution ADC available)

> [!warning] MCC Fabric Clock vs ADC Clock
> **CustomInstrument uses the MCC Fabric Clock**, not the ADC/DAC sample rate. Timing registers (durations, delays) must be calculated using the fabric clock period.

| Platform | MCC Period | ADC Resolution (approx) |
|----------|------------|------------------------|
| Moku:Go | 32 ns | 1/6550.4 V/bit |
| Moku:Lab | 8 ns | 2/30000 V/bit |
| Moku:Pro | 3.2 ns | 1/29925 V/bit |
| Moku:Delta | 0.8 ns | 1/36440 V/bit |

**Runtime Platform Discovery:**
```python
description = m.describe()
hardware = description['hardware']  # e.g., 'Moku:Go', 'Moku:Pro'

# Clock period lookup (seconds)
period_dict = {
    'Moku:Go': 32e-9,
    'Moku:Lab': 8e-9,
    'Moku:Pro': 3.2e-9,
    'Moku:Delta': 3.2e-9
}

# ADC resolution lookup (volts per bit)
resolution_dict = {
    'Moku:Go': 1/6550.4,
    'Moku:Lab': 2/30000,
    'Moku:Pro': 1/29925,
    'Moku:Delta': 1/36440
}

period = period_dict[hardware]
resolution = resolution_dict[hardware]
```

# Common Usage Patterns

## Basic Multi-Instrument Setup

```python
from moku.instruments import CustomInstrument, MultiInstrument, Oscilloscope

# Connect to Moku
m = MultiInstrument('192.168.1.100', platform_id=4, force_connect=True)

try:
    # Deploy custom instrument and oscilloscope
    bitstream = "path/to/project/bitstreams.tar.gz"
    mc = m.set_instrument(1, CustomInstrument, bitstream=bitstream)
    osc = m.set_instrument(2, Oscilloscope)

    # Configure signal routing
    connections = [
        dict(source="Input1", destination="Slot1InA"),
        dict(source="Input2", destination="Slot1InB"),
        dict(source="Slot1OutA", destination="Slot2InA"),
        dict(source="Slot1OutB", destination="Slot2InB"),
    ]
    m.set_connections(connections=connections)

    # Configure frontend
    m.set_frontend(1, '50Ohm', 'DC', attenuation='0dB')
    m.set_frontend(2, '50Ohm', 'DC', attenuation='0dB')

    # Set control registers with unit conversion
    trigger_level_volts = 0.5
    trigger_delay_ns = 100

    mc.set_control(0, int(trigger_level_volts / resolution))
    mc.set_control(1, int(trigger_delay_ns * 1e-9 / period))

    # Read status registers
    status = mc.get_status()
    print(f"Status: {status}")

finally:
    m.relinquish_ownership()
```

## Multi-Channel Register Offset Pattern

For multi-channel designs, use arithmetic offsets between channels:

```python
# Dual-channel boxcar example: Channel 0 uses CR0-4, Channel 1 uses CR5-9
CHANNEL_OFFSET = 5

# Configure both channels with same settings
for channel in [0, 1]:
    base = channel * CHANNEL_OFFSET
    mc.set_control(base + 0, trigger_level)
    mc.set_control(base + 1, trigger_delay)
    mc.set_control(base + 2, gate_width)
    mc.set_control(base + 3, avg_length)
    mc.set_control(base + 4, output_gain)
```

## Mode Register as Output Multiplexer

A common pattern uses a single control register to select output modes:

```python
# CR15 acts as output mode selector
match selected_mode:
    case 'Align':
        mc.set_control(15, 15)   # Alignment mode
    case 'Output_Ch0':
        mc.set_control(15, 7)    # Output channel 0
    case 'Output_Both':
        mc.set_control(15, 4)    # Dual output
```

## Status Polling Pattern

```python
import time

def wait_for_status_flag(mc, status_idx, bit_mask, timeout=5.0):
    """Wait for a specific status flag to be set."""
    start = time.time()
    while time.time() - start < timeout:
        status = mc.get_status()
        if status.get(f'status{status_idx}', 0) & bit_mask:
            return True
        time.sleep(0.01)
    return False

# Usage
mc.set_control(0, 1)  # Start acquisition
if wait_for_status_flag(mc, 0, 0x01):  # Wait for done flag
    print("Acquisition complete")
else:
    print("Timeout waiting for acquisition")
```

# Migration from CloudCompile

If upgrading from 4.0.x to 4.1.1.1:

```python
# OLD (4.0.x) - still works but deprecated
from moku.instruments import CloudCompile
cc = m.set_instrument(1, CloudCompile, bitstream=path)

# NEW (4.1.1.1) - recommended
from moku.instruments import CustomInstrument
mc = m.set_instrument(1, CustomInstrument, bitstream=path)

# Status registers - NEW capability
status = mc.get_status()  # Not available in CloudCompile
```

# See Also

- [cloudcompile.md](cloudcompile.md) - Previous version documentation (deprecated)
- [mim.md](mim.md) - MultiInstrument class for slot management
- [Moku Compile Documentation](https://apis.liquidinstruments.com/mc/) - Official MC docs
- [Official Moku API Documentation](https://apis.liquidinstruments.com/starting.html)

# Example Files

From the official moku-examples repository:

| Example | Description |
|---------|-------------|
| `python-api/custom_instrument_adder.py` | Basic adder with MIM + Oscilloscope |
| `python-api/custom_instrument_arithmetic.py` | Mode selection via control registers |
| `mc/HDLCoder/hdlcoder_boxcar/python/BoxcarControlPanel.py` | Full GUI with platform detection |
| `mc/HDLCoder/hdlcoder_boxcar/python/DualBoxcarControlPanel.py` | Multi-channel register patterns |
| `mc/Moderate/SweptPulse/MokuPro/mim_mpro_wg_mcc_la_osc.py` | 4-slot MIM with WG + MC + DL + Osc |

---
**View this document:**
- 📖 [Obsidian Publish](https://publish.obsidian.md/dpd-001/moku_md/instruments/custominstrument)
- 💻 [GitHub](https://github.com/sealablab/DPD-001/blob/main/moku_md/instruments/custominstrument.md)
- ✏️ [Edit on GitHub](https://github.com/sealablab/DPD-001/edit/main/moku_md/instruments/custominstrument.md)

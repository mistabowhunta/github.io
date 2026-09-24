---
title: " Taming the FLIR Lepton SPI Bus on a Headless Drone"
name: "Fault-Tolerant SPI Architecture for Headless Drones"
description: "How to prevent FLIR Lepton VoSPI lockups on a headless Raspberry Pi drone using daemonized Python fault tolerance"
tools: [python, hardware, spi, aerotherm, drone, flir, raspberry-pi]
date: 2026-09-24
---

<div class="card mb-4">
  <div class="card-body">
    <h4 class="card-title border-bottom pb-2">Issue Summary</h4>
    <p>
      Integrating a FLIR Lepton 3.5 via a PureThermal breakout board onto a headless 1588g drone presents a critical failure point. If the Python radiometric script encounters a fatal error or is killed unexpectedly, it leaves the SPI file descriptor open. The sensor locks the bus, the stream dies, and the headless Pi cannot re-establish the connection without a physical power cycle mid-flight.
    </p>
    <div class="card bg-transparent">
      <div class="card-body p-0">
        <h4 class="card-title border-bottom pb-2">Hardware & Software</h4>
        <ol class="mt-3">
          <li class="mb-2 bg-transparent"><strong>Hardware:</strong> Raspberry Pi 3 A+, FLIR Lepton 3.5, Custom AeroTherm Chassis</li>
          <li class="mb-2 bg-transparent"><strong>Software Stack:</strong> Python, spidev library, systemd, Linux</li>
        </ol>
      </div>
    </div>
    <h4 class="card-title border-bottom pb-2">Fault Tolerance Protocol</h4>
    <ol class="mt-3">
      <li class="mb-2"><strong>Initialize the Bus:</strong> Open the SPI port and explicitly define the max speed (16MHz) for the VoSPI stream.</li>
      <li class="mb-2"><strong>Isolate Execution:</strong> Wrap the core extraction logic inside a dedicated daemonized thread utilizing a strict try/except block.</li>
      <li class="mb-2"><strong>Enforce Hardware Release:</strong> Utilize a <code>finally</code> block to guarantee the <code>spi.close()</code> command executes, even on a fatal crash, releasing the bus for immediate systemd restart.</li>
    </ol>
    <h4 class="card-title border-bottom pb-2 mt-4">Example</h4>
      <p class="mb-2">This is the bare-metal logic used to ensure the sensor re-initializes successfully during a mid-flight daemon crash:</p>
  </div>
</div>
```python
import spidev

def thermal_capture_thread():
    spi = spidev.SpiDev()
    spi.open(0, 0)
    spi.max_speed_hz = 16000000

    try:
        while True:
            frame = get_lepton_frame(spi)
            process_thermal_data(frame)
    except Exception as e:
        log_fatal_error(e)
    finally:
        # Guaranteed execution to release hardware
        spi.close()
        print("SPI bus released. Ready for safe restart.")
```

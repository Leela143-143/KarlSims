# Evolved Virtual Creatures (Karl Sims Project Recreation)

This project is a recreation and extension of Karl Sims' groundbreaking 1994 work on "Evolved Virtual Creatures." It utilizes C++, DirectX 9 for rendering, NVIDIA PhysX 3.3.0 for physics simulation, a custom genetic algorithm, neural networks for creature control, and a genotype script parser for defining creature morphology.

- **Original Karl Sims Project:** [http://www.karlsims.com/evolved-virtual-creatures.html](http://www.karlsims.com/evolved-virtual-creatures.html)
- **Developer's Project Page & Wiki:** [http://jjuiddong.co.kr/wiki/index.php/Evolved_Virtual_Creatures](http://jjuiddong.co.kr/wiki/index.php/Evolved_Virtual_Creatures)
- **Summary Paper/Docs:** [https://github.com/jjuiddong/KarlSims/wiki](https://github.com/jjuiddong/KarlSims/wiki)
- **Genotype Script Info:** [https://github.com/jjuiddong/KarlSims/wiki/Genotype-Script](https://github.com/jjuiddong/KarlSims/wiki/Genotype-Script)

## Features
*   Physics-based simulation of virtual creatures.
*   Evolution of creature morphology and behavior using genetic algorithms.
*   Neural network brains for creature control.
*   Creature definition via a text-based genotype script.
*   **NEW:** Support for defining creature body parts using custom 3D OBJ meshes.

## Building from Source

To compile the Evolved Virtual Creatures project yourself, you'll need Git and Microsoft Visual Studio (the project files are for VS2010, but newer versions of Visual Studio can typically upgrade them).

### 1. Clone the Repository

If you haven't already, clone the project repository from GitHub:

```bash
git clone https://github.com/jjuiddong/EvolvedVirtualCreatures.git
cd EvolvedVirtualCreatures
```

### 2. Configure Dependencies

*   **NVIDIA PhysX 3.3.0 SDK:** This project **requires** the NVIDIA PhysX 3.3.0 SDK.
    *   You will need to obtain this specific version of the SDK.
    *   Ensure the environment variable `PHYSX_DIR` is set to the root directory of your PhysX 3.3.0 SDK installation *before* opening Visual Studio. For example, `PHYSX_DIR=C:\NVIDIA\PhysX_3.3.0_SDK`. (Note: Use backslashes for environment variables on Windows, and ensure they are properly escaped if setting via some scripts).
    *   The Visual Studio projects are configured to use this variable to find PhysX headers and libraries. If you still encounter issues, you may need to manually verify/update the Include and Library Directories in the project properties within Visual Studio (Right-click on projects like `PhysXSample`, `EvolvedVirtualCreatures` -> Properties -> VC++ Directories).
*   **DirectX 9.0 SDK:** Ensure you have the DirectX 9.0 SDK installed, as the project uses D3D9 for rendering. Your system likely has DirectX runtimes, but for development, the SDK is needed.
*   **Boost Libraries (1.55.0):** The project notes specify Boost version 1.55.0. If not installed or if paths are incorrect, you might encounter build issues related to Boost. Project settings may need adjustment to point to your Boost installation root (e.g., `BOOST_ROOT` environment variable or direct paths in project settings).

### 3. Open the Solution in Visual Studio

*   Navigate to the `compiler` directory within the cloned project.
*   You'll find two solution versions:
    *   `vc10win32/KarlSims.sln` (for 32-bit builds)
    *   `vc10win64/KarlSims.sln` (for 64-bit builds)
*   Double-click the `.sln` file of your choice. If using a newer Visual Studio version, it might prompt to retarget/upgrade the project – this is usually acceptable.

### 4. Select Build Configuration

*   In Visual Studio:
    *   **Solution Configuration:** Choose "Debug" (for testing/development) or "Release".
    *   **Solution Platform:** "Win32" or "x64", matching the solution file opened.

### 5. Build the Solution

*   From the Visual Studio menu, select "Build" -> "Build Solution".
*   Monitor the "Output" window for progress and errors. Address any dependency issues (PhysX, DirectX, Boost) if they arise.

### Output Location

Successfully compiled executables are typically placed in a `bin` directory relative to the solution's directory (e.g., if solution is in `compiler/vc10win32/`, exe might be `../../bin/vc10win32/debug/EvolvedVirtualCreatures.exe` which translates to `EvolvedVirtualCreatures/bin/vc10win32/debug/EvolvedVirtualCreatures.exe` from the project root).

## Running the Simulation

### Executable

Run the `EvolvedVirtualCreatures.exe` from the output location determined during the build.

### Selecting a Genotype File

Creatures are defined by genotype script files (`.txt`). To specify which creature design to load:

1.  **Edit `CEvc::onInit()`:** This method is in `EvolvedVirtualCreatures/EvolvedVirtualCreatures.cpp`.
2.  **Modify Creature Generation Call:** Inside `onInit()`, find code that generates the initial creature(s), for example:
    ```cpp
    // In CEvc::onInit()
    PxVec3 pos(0.0f, 2.0f, 0.0f);
    m_Creatures.push_back(new CCreature(*this));
    m_Creatures.back()->GenerateImmediate("genotype.txt", pos, NULL, 2);
    ```
3.  **Change Filename:** To load a different creature, change the filename in the `GenerateImmediate` call. For instance, to load the sample using a custom mesh:
    ```cpp
    m_Creatures.back()->GenerateImmediate("genotype_custom_mesh_creature.txt", pos, NULL, 2);
    ```
    Standard genotype files (like `genotype.txt`, `genotype_box.txt`, `genotype_custom_mesh_creature.txt`) are located in the project's root directory and are typically loaded from there.

## Defining Creatures with Genotype Scripts

Refer to the [Genotype Script Wiki](https://github.com/jjuiddong/KarlSims/wiki/Genotype-Script) for the basic syntax.

### Using Custom 3D Meshes for Creature Parts

You can define creature body parts using custom 3D meshes for more detailed visuals.

**Genotype `meshfile` Attribute:**

Use the `meshfile` attribute within a body part definition in your genotype script:

**Example:**
```genotype
MyLimb (
    shape box,  // Fallback, ignored if meshfile is valid
    meshfile "resource/my_limb.obj", // Path to your OBJ mesh
    dimension (1.0, 1.5, 1.0),     // Scale for the OBJ (X, Y, Z)
    material (0.8, 0.4, 0.2),      // Diffuse color (R, G, B)
    mass 2.5                       // Physical mass
);
```

**Details:**

*   **`meshfile "path/to/mesh.obj"`:**
    *   Path to a Wavefront OBJ (`.obj`) file. Relative paths (e.g., `resource/mesh.obj`) are recommended. The sample `custom_limb_tri.obj` is in the `resource/` directory. Ensure the path is correct relative to the application's working directory when it runs.
*   **Supported Format:** Only **OBJ (.obj)** files. Meshes should be **triangulated** for reliable physics collision.
*   **`dimension (X, Y, Z)`:** Acts as a **scale factor** for the loaded OBJ. `(1,1,1)` is original size.
*   **`material (R, G, B)`:** Defines the diffuse color (0.0 to 1.0 range for components). Materials from the OBJ/MTL file are currently ignored.
*   **Physics:** `mass` and other physical properties apply. Collision shapes are generated from the mesh geometry. If `mass` is <= 0, the part will be kinematic (not affected by physics forces directly).

## Additional Configuration & Tools

### PhysX Specifics
*   **No NVidia Graphic Card Computer:** If running on a machine without an NVIDIA GPU that supports PVD (PhysX Visual Debugger), you might need to `define RENDERER_PVD` in the `SampleBase` and `SampleRenderer` projects' preprocessor definitions to disable certain PVD-GPU interactions, or ensure PVD connection is not attempted if it causes issues.

### wxMemMonitor (Memory Monitoring Tool)
The project includes `wxMemMonitorLib` for memory monitoring during development.
*   **Dependency:** Requires wxWidgets 3.0.0.
*   **Usage:**
    ```cpp
    // Include the header
    #include "../wxMemMonitorLib/wxMemMonitor.h"

    // In your main application startup:
    MEMORYMONITOR_INNER_PROCESS(); // Or other modes as per wxMemMonitor docs
    if (!memmonitor::Init(memmonitor::INNER_PROCESS, hInstance, "config_target.json"))
    {
        MessageBoxA(NULL, memmonitor::GetLastError().c_str(), "ERROR", MB_OK);
    }

    // Before application exit:
    memmonitor::Cleanup();
    ```
*   **Config File (`config_evc.json`):**
    *   Place this JSON file in the runtime directory (e.g., `bin/vc10win32/debug/`).
    *   Contents:
        ```json
        {
            "pdbpath" : "EvolvedVirtualCreaturesDEBUG.pdb",
            "sharedmemoryname" : "EVC"
        }
        ```
        (Adjust `pdbpath` to the actual PDB file name and location for your build if needed.)
*   **wxWidgets `floor` Ambiguity:** If you encounter a compiler error about an ambiguous 'floor' function when building wxWidgets or the project with it, you may need to modify `wxWidgets/include/wx/geometry.h` to change `floor` to `::floor` to specify the global namespace version.

## License
MIT
```

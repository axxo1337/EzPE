# EzPE

## What is this?

A portable executable (PE) header-only utility library.

## Usage

EzPE is a header-only library. Simply include `EzPE.h` in your project:

```cpp
#include "EzPE.h"
```

### Quick Example

Below is a simple example snippet demonstrating how to load a PE file, find exported functions, and inspect section headers:

```cpp
// Load PE from disk
EzPE::PE pe("target.exe");

if (!pe.getIsLoaded())
{
  std::printf("Failed to load PE: %s\n", pe.getErrorMessage().c_str());
  return;
}

// Find an exported function
void* p_func{ pe.getExportedFunction("SomeExportedFunction") };
if (p_func)
  std::printf("Found export at: %p\n", p_func);

// Find and inspect a section
IMAGE_SECTION_HEADER* p_code_section{ pe.findSectionByName(".text") };
if (p_code_section)
{
  std::printf(".text VirtualAddress: 0x%X\n", p_code_section->VirtualAddress);

  uint8_t* p_code_section_data{ pe.getSectionData(*p_code_section) };
  // ...
}

// Clears the PE out of memory
pe.clear();
```

### Loading a PE Image

You can load a Portable Executable from a file on disk, from memory (e.g., a loaded module), or from a Windows resource:

```cpp
// 1. Load from a file on disk
EzPE::PE pe_file("sample.exe", EzPE::PE_Properties::DATA);

// 2. Load from memory (e.g., currently running module)
HMODULE h_module{ GetModuleHandleA(nullptr) };
EzPE::PE pe_mem(h_module, EzPE::PE_Properties::MAPPED | EzPE::PE_Properties::DATA);

// 3. Load from a Windows resource
HRSRC h_resource{ FindResourceA(nullptr, MAKEINTRESOURCEA(101), RT_RCDATA) };
EzPE::PE pe_res(h_resource, EzPE::PE_Properties::DATA);
```

### Inspecting Headers & Properties

Once loaded, you can access standard Win32 PE header structures directly:

```cpp
EzPE::PE pe("sample.exe");

if (pe.getIsLoaded())
{
  // Access DOS Header
  WORD dos_magic{ pe.p_dos_header->e_magic };

  // Access File Header
  WORD num_sections{ pe.p_file_header->NumberOfSections };

  // Access Optional Header
  DWORD entry_point{ pe.p_optional_header->AddressOfEntryPoint };
  ULONG_PTR image_base{ pe.p_optional_header->ImageBase };

  // Get calculated physical size
  size_t total_size{ pe.getPhysicalSize() };
}
```

### Working with Sections

Find sections by name, access section data, or insert new sections:

```cpp
EzPE::PE pe("sample.exe");

if (pe.getIsLoaded())
{
  // Find a section by name
  IMAGE_SECTION_HEADER* p_text_sec{ pe.findSectionByName(".text") };
  if (p_text_sec)
  {
    uint8_t* p_sec_data{ pe.getSectionData(*p_text_sec) };
    size_t raw_size{ p_text_sec->SizeOfRawData };
  }

  // Insert a new section into an unmapped PE
  EzPE::PE::Section(pe)
    .name(".ext")
    .data(0x1000 /* virtual_size */, 0x200 /* file_size */)
    .characteristics(IMAGE_SCN_CNT_INITIALIZED_DATA | IMAGE_SCN_MEM_READ)
    .insert();
}
```

### Error Handling

EzPE provides built-in error checking for operations:

```cpp
EzPE::PE pe("sample.exe");

if (pe.getHasError())
{
  std::printf("Error: %s\n", pe.getErrorMessage().c_str());
}
```

## Contributing

This project is free and open source and will remain so forever. You are welcome to contribute. Simply make a pull request for whatever it is you would like to add, but
there are a few things you need to keep in mind:

1. C++17 only for now.
2. snake_case for variable names, PascalCase for namespaces and structures and camelCase for methods and function names (there might be more specefics so please just
   base your style on the already existing code).
3. Make an issue describing your feature or bugfix and specify your intent to address said issue. Once you make the pull request you can auto-close the issue by doing `close #<issue_number_here>`.
4. When making a branch give it a meaningful name like `feature/name_of_feature` or `fix/name_of_fix`.

## License

This project is licensed under the MIT License with some specifications - see the [LICENSE](LICENSE) file for details.

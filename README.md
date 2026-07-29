# EzPE

## What is this?

A portable executable (PE) header-only utility library.

## Usage
```cpp
// A simple example about parsing a PE file.

EzPE::PE pe("target.exe");

void* func = pe.getExportedFunction("SomeExportedFunction");
if (func)
  printf("Found export at: %p\n", func);

IMAGE_SECTION_HEADER* section = pe.findSectionByName(".text");
if (section)
  printf(".text VirtualAddress: 0x%X\n", section->VirtualAddress);

if (section)
{
  uint8_t* data = pe.getSectionData(*section);
  // ...
}

pe.clear();
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

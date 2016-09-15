# Definition of Done (DoD)
Following is the checklist of activities that add verifiable/demonstrable value to the product.
Each and every delivery to SDL should be verified for readiness upon this checklist.
DoD is not static; it evolves with SDL.
It is required to use this document as a checklist each time preparing delivery.

## Delivery completeness checklist
Complete delivery to SDL consist of following components:
1. Source codes
2. Code comments
3. Unit tests (UTs)
4. Integration tests (for [ATF][ATF-LINK])
5. SDD/SAD updates
6. Other guidelines

### Source codes
Main language of SDL is C++ [ISO/IEC 14882:2003 «Standard for the C++ Programming Language»](http://www.open-std.org/jtc1/sc22/wg21/docs/standards).<br>
Code must comply Google coding styles with some specifics ([SDL Coding Style](https://github.com/smartdevicelink/sdl_core/wiki/SDL-Coding-Style-Guide)).<br>
*Readiness criteria*: **Compilable (GCC compiler version 4.4)**<br>
*Acceptance check*: **Automated on CI/CD server**<br>

### Code comments
Comment blocks of provided source codes must comply [Doxygen](http://www.stack.nl/~dimitri/doxygen/index.html) requirements.<br>
Further this information will be used to build detailed SDL help. <br>
Detailed description how to annotate your C++ can be found on [Doxygen web site](http://www.stack.nl/~dimitri/doxygen/manual/docblocks.html#docstructure).<br>

*Readiness criteria*: **No new SCA findings**<br>
*Acceptance check*: **Automated on CI/CD server**<br>

Currently additional SCA rules are being developed and might be added to DoD in future.

### Unit tests

SDL "Unit" is class interface (set of public methods).
Commonly accepted rule in SDL is 65% coverage of lines.
However full coverage makes SDL more stable and easier to maintain.
All UTs can be added/updated at following location 
>https://github.com/smartdevicelink/sdl_core/tree/**\<branch_name\>**/src/components/**\<component_name\>**/test<br>

SDL uses [**Google Test**](https://github.com/google/googletest/tree/master/googletest) and [**Google Mock**](https://github.com/google/googletest/tree/master/googlemock) for testing and isolation.

*Readiness criteria*: **Compilable, 100% of tests passed, overall lines coverage is not lower than 65%**<br>
*Acceptance check*: **Automated on CI/CD server**<br>

### Integration tests

For integration testing a special testing tool was [created][ATF-LINK].
This tool uses scripts written on Lua to run integration test.

*Readiness criteria*: **100% of test are passed**<br>
*Acceptance check*: **[CCB][CCB-LINK] review checkpoint; automated test run on CI/CD server**<br>

### SDD/SAD updates

Detailed description how to create or update SDD/SAD available in [SDL Wiki](https://github.com/smartdevicelink/sdl_core/wiki/Providing-design-documentation-with-code-changes).

>**NOTE:** SDD/SAD review might take up to two working days. 
>Delivery schedule include at least two days for documentation review.

*Readiness criteria*: **CCB review approval**<br>
*Acceptance check*: **[CCB][CCB-LINK] review checkpoint**<br>

### Other guidelines

SDL contains set of repositories with guidelines which helps developers properly maintain the product.
Adding new features to SDL or solving certain difficult problem require additional description in product Wiki.
If article is too large for a wiki page you may create a separate set of documents in respective product part guides repository. <br>
Product part guide repositories are named like: **sdl_\<product_part\>_guides** or **sdl_\<product_part\>_integration_guidelines**.

>**NOTE:** Guidelines review might take up to two working days. 
>Delivery schedule include at least two days for documentation review.

*Readiness criteria*: **[CCB][CCB-LINK] review approval**<br>
*Acceptance check*: **[CCB][CCB-LINK] review checkpoint**<br>

[ATF-LINK]: https://github.com/smartdevicelink/sdl_atf "Automated Test Framework"
[CCB-LINK]: ChangeControlBoard.md "Change Control Board"

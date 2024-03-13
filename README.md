# WindowsMessages

**WindowsMessages.pp** is a Free Pascal (FPC) unit that provides a set of functions for retrieving Windows messages.

## License

**WindowsMessages.pp** is covered by the GNU Lesser Public License (LPGL). See the file `LICENSE` for details.

## Version History

**1.0.0 (2024-03-13)**

* Initial release.

## Reference

> IMPORTANT: The unit enables FPC's UNICODESTRINGS mode, so all references to `string` are `UnicodeString`.

### GetWindowsMessage

Gets a message string from a buffer or message table.

#### Syntax

    function GetWindowsMessage(MessageID: DWORD): string;

    function GetWindowsMessage(MessageID: DWORD; AddId: Boolean): string;

    function GetWindowsMessage(MessageID: DWORD; Module: string): string;

    function GetWindowsMessage(MessageID: DWORD; Module: string; AddId: Boolean);

    function GetWindowsMessage(MessageID: DWORD; Args: array of string): string;

    function GetWindowsMessage(MessageID: DWORD; AddId: Boolean; Args: array of string): string;

    function GetWindowsMessage(MessageID: DWORD; Module: string; Args: array of string): DWORD;

    function GetWindowsMessage(MessageID: DWORD; Module: string; AddId: Boolean; Args: array of string): string;

#### Parameters

`MessageID`

Specifies the message identifier for which you want to retrieve the message string.

`AddId`

The function will append the numeric message identifier, in parentheses, at the end of the returned message string. This parameter is optional.

`Module`

Specifies the name of a DLL file containing message identifiers and associated strings. This parameter is optional.

`Args`

Specifies an array of format strings to replace numbered escape sequence strings in the message string. This parameter is optional. (See **Remarks**, below, for an important caveat concerning the use of the `Args` array.)

#### Return Value

If the function succeeds, the return value will be the message string with requested modifications:

* If the `AddId` parameter is `true`, the function will append the numeric message identifier, in parentheses, at the end of the returned message string.

* If you specify an `Args` array, the function will substitute the numbered escape sequence strings in the message string with the corresponding elemements of the `Args` array, in sequence. For example, if the message string is **%1 is not a valid Win32 application**, then the function will substitute **%1** in the message string with the first element from the `Args` array.  (See **Remarks**, below, for an important caveat concerning the use of the `Args` array.)

If the function fails to find a message string corresponding to the message identifier, it will return the string **Unknown error** (with the 
trailing message ID in parentheses if the `AddId` parameter is `true`).

#### Remarks

If you use the `Args` array, it must contain at least the number of elements as the number of numbered escape sequence strings in the message string. That is, if the message string contains the numbered escape sequence **%1**, the `Args` array should contain at least one element; if the message string contains **%2**, the `Args` array should contain at least two elements, and so forth. If the message string contains a greater number of numbered escape sequences than the number of elements in the `Args` array, the behavior is undefined and will result in garbage data or program errors. For this reason, do not use the `Args` array unless you know beforehand the exact number of numbered escape sequence strings in the message string and provide an `Args` array with at least that number of elements.

#### Examples

The return strings in the code comments below are U. S. English:

    GetWindowsMessage(0);
    // Returns the string 'The operation completed successfully.'

    GetWindowsMessage(5, true);
    // Returns the string 'Access is denied. (5)'.

    GetWindowsMessage(12031, 'wininet.dll');
    // Returns the string 'The connection with the server was reset'

The following example illustrates the use of the `Args` array:

    var
      Args: array of string;

    ...

    SetLength(Args, 1);
    Args[0] := '"test"';
    GetWindowsMessage(ERROR_BAD_EXE_FORMAT, Args);
    // Returns the string '"test" is not a valid Win32 application.'

The U. S. English message string for `ERROR_BAD_EXE_FORMAT` (error code 193, 0xC1) is the following:

    %1 is not a valid Win32 application.

The `Args` array contains a single element, so the **GetWindowsMessage** function replaces the numbered escape sequence **%1** in the message string with the first element from the `Args` array (the string `"test"`).

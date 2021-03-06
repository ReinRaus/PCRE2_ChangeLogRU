# Лог изменений для PCRE2  

--------------------

  |   |   |
:---:|:------|:------:
   | Version **10.21** от 12 Января 2016г | 
1. | Improve matching speed of patterns starting with + or * in JIT. | производительность
2. | Use `memchr()` to find the first character in an unanchored match in 8-bit mode in the interpreter. This gives a significant speed improvement. | производительность
3. | Removed a redundant copy of the opcode\_possessify table in the `pcre2_auto_possessify.c` source. | 
4. | Fix typos in `dftables.c` for z/OS.
5. | Change 36 for 10.20 broke the handling of `[[:>:]]` and `[[:<:]]` in that processing them could involve a buffer overflow if the following character was an opening parenthesis. | 
6. | Change 36 for 10.20 also introduced a bug in processing this pattern: `/((?x)(*:0))#(?'/`. Specifically: if a setting of `(?x)` was followed by a `(*MARK)` setting (which `(*:0)` is), then `(?x)` did not get unset at the end of its group during the scan for named groups, and hence the external `#` was incorrectly treated as a comment and the invalid `(?'` at the end of the pattern was not diagnosed. This caused a buffer overflow during the real compile. This bug was discovered by Karl Skomski with the LLVM fuzzer. | 
7. | Moved the `pcre2_find_bracket()` function from `src/pcre2_compile.c` into its own source module to avoid a circular dependency between `src/pcre2_compile.c` and `src/pcre2_study.c` | 
8. | A callout with a string argument containing an opening square bracket, for example `/(?C$[$)(?<]/`, was incorrectly processed and could provoke a buffer overflow. This bug was discovered by Karl Skomski with the LLVM fuzzer. | 
9. | The handling of callouts during the pre-pass for named group identification has been tightened up. | 
10. | The quantifier `{1}` can be ignored, whether greedy, non-greedy, or possessive. This is a very minor optimization. | производительность
11. | A possessively repeated conditional group that could match an empty string, for example, `/(?(R))*+/`, was incorrectly compiled. | bug fix
12. | The Unicode tables have been updated to Unicode 8.0.0 (thanks to Christian Persch). |
13. | An empty comment `(?#)` in a pattern was incorrectly processed and could provoke a buffer overflow. This bug was discovered by Karl Skomski with the LLVM fuzzer. | bug fix
14. | Fix infinite recursion in the JIT compiler when certain patterns such as `/(?:|a|){100}x/` are analysed. |
15. | Some patterns with character classes involving `[:` and `\\` were incorrectly compiled and could cause reading from uninitialized memory or an incorrect error diagnosis. Examples are: `/[[:\\](?<[::]/ and /[[:\\](?'abc')[a:]`. The first of these bugs was discovered by Karl Skomski with the LLVM fuzzer. | 
16. | Pathological patterns containing many nested occurrences of `[:` caused `pcre2_compile()` to run for a very long time. This bug was found by the LLVM fuzzer. |
17. | A missing closing parenthesis for a callout with a string argument was not being diagnosed, possibly leading to a buffer overflow. This bug was found by the LLVM fuzzer. | 
18. | A conditional group with only one branch has an implicit empty alternative branch and must therefore be treated as potentially matching an empty string. | 
19. | If `(?R` was followed by `-` or `+` incorrect behaviour happened instead of a diagnostic. This bug was discovered by Karl Skomski with the LLVM fuzzer. | 
20. | Another bug that was introduced by change 36 for 10.20: conditional groups whose condition was an assertion preceded by an explicit callout with a string argument might be incorrectly processed, especially if the string contained `\Q`. This bug was discovered by Karl Skomski with the LLVM fuzzer. |
21. | Compiling PCRE2 with the sanitize options of clang showed up a number of very pedantic coding infelicities and a buffer overflow while checking a UTF-8 string if the final multi-byte UTF-8 character was truncated. | 
22. | For Perl compatibility in EBCDIC environments, ranges such as a-z in a class, where both values are literal letters in the same case, omit the non-letter EBCDIC code points within the range. |
23. | Finding the minimum matching length of complex patterns with back references and/or recursions can take a long time. There is now a cut-off that gives up trying to find a minimum length when things get too complex. |
24. | An optimization has been added that speeds up finding the minimum matching length for patterns containing repeated capturing groups or recursions. |
25. | If a pattern contained a back reference to a group whose number was duplicated as a result of appearing in a `(?|...)` group, the computation of the minimum matching length gave a wrong result, which could cause incorrect "no match" errors. For such patterns, a minimum matching length cannot at present be computed. |
26. | Added a check for integer overflow in conditions `(?(<digits>)` and `(?(R<digits>)`. This omission was discovered by Karl Skomski with the LLVM fuzzer. |
27. | Fixed an issue when `\p{Any}` inside an xclass did not read the current character. |
28. | If `pcre2grep` was given the `-q` option with `-c` or `-l`, or when handling a binary file, it incorrectly wrote output to stdout. |
29. | The JIT compiler did not restore the control verb head in case of `*THEN` control verbs. This issue was found by Karl Skomski with a custom LLVM fuzzer. |
30. | The way recursive references such as `(?3)` are compiled has been re-written because the old way was the cause of many issues. Now, conversion of the group number into a pattern offset does not happen until the pattern has been completely compiled. This does mean that detection of all infinitely looping recursions is postponed till match time. In the past, some easy ones were detected at compile time. This re-writing was done in response to yet another bug found by the LLVM fuzzer. |
31. | A test for a back reference to a non-existent group was missing for items such as `\987`. This caused incorrect code to be compiled. This issue was found by Karl Skomski with a custom LLVM fuzzer. |
32. | Error messages for syntax errors following `\g` and `\k` were giving inaccurate offsets in the pattern. |
33. | Improve the performance of starting single character repetitions in JIT. |
34. | `(*LIMIT_MATCH=)` now gives an error instead of setting the value to `0`. |
35. | Error messages for syntax errors in `*LIMIT_MATCH` and `*LIMIT_RECURSION` now give the right offset instead of zero. |
36. | The JIT compiler should not check repeats after a `{0,1}` repeat byte code. This issue was found by Karl Skomski with a custom LLVM fuzzer. |
37. | The JIT compiler should restore the control chain for empty possessive repeats. This issue was found by Karl Skomski with a custom LLVM fuzzer. |
38. | A bug which was introduced by the single character repetition optimization was fixed. |
39. | Match limit check added to recursion. This issue was found by Karl Skomski with a custom LLVM fuzzer. |
40. | Arrange for the UTF check in `pcre2_match()` and `pcre2_dfa_match()` to look only at the part of the subject that is relevant when the starting offset is non-zero. |
41. | Improve first character match in JIT with SSE2 on x86. |
42. | Fix two assertion fails in JIT. These issues were found by Karl Skomski with a custom LLVM fuzzer. |
43. | Correct the setting of `CMAKE_C_FLAGS` in `CMakeLists.txt` (patch from Roy Ivy III). |
44. | Fix bug in `RunTest.bat` for new test 14, and adjust the script for the added test (there are now 20 in total). |
45. | Fixed a corner case of range optimization in JIT. |
46. | Add the `${*MARK}` facility to `pcre2_substitute()`. |
47. | Modifier lists in `pcre2test` were splitting at spaces without the required commas. |
48. | Implemented `PCRE2_ALT_VERBNAMES`. |
49. | Fixed two issues in JIT. These were found by Karl Skomski with a custom LLVM fuzzer. |
50. | The `pcre2test` program has been extended by adding the `#newline_default` command. This has made it possible to run the standard tests when PCRE2 is compiled with either `CR` or `CRLF` as the default newline convention. As part of this work, the new command was added to several test files and the testing scripts were modified. The `pcre2grep` tests can now also be run when there is no `LF` in the default newline convention.

51. The RunTest script has been modified so that, when JIT is used and valgrind
is specified, a valgrind suppressions file is set up to ignore "Invalid read of
size 16" errors because these are false positives when the hardware supports
the SSE2 instruction set.

52. It is now possible to have comment lines amid the subject strings in
pcre2test (and perltest.sh) input.

53. Implemented PCRE2_USE_OFFSET_LIMIT and pcre2_set_offset_limit().

54. Add the null_context modifier to pcre2test so that calling pcre2_compile()
and the matching functions with NULL contexts can be tested.

55. Implemented PCRE2_SUBSTITUTE_EXTENDED.

56. In a character class such as [\W\p{Any}] where both a negative-type escape
("not a word character") and a property escape were present, the property
escape was being ignored.

57. Fixed integer overflow for patterns whose minimum matching length is very,
very large.

58. Implemented --never-backslash-C.

59. Change 55 above introduced a bug by which certain patterns provoked the
erroneous error "\ at end of pattern".

60. The special sequences [[:<:]] and [[:>:]] gave rise to incorrect compiling
errors or other strange effects if compiled in UCP mode. Found with libFuzzer
and AddressSanitizer.

61. Whitespace at the end of a pcre2test pattern line caused a spurious error
message if there were only single-character modifiers. It should be ignored.

62. The use of PCRE2_NO_AUTO_CAPTURE could cause incorrect compilation results
or segmentation errors for some patterns. Found with libFuzzer and
AddressSanitizer.

63. Very long names in (*MARK) or (*THEN) etc. items could provoke a buffer
overflow.

64. Improve error message for overly-complicated patterns.

65. Implemented an optional replication feature for patterns in pcre2test, to
make it easier to test long repetitive patterns. The tests for 63 above are
converted to use the new feature.

66. In the POSIX wrapper, if regerror() was given too small a buffer, it could
misbehave.

67. In pcre2_substitute() in UTF mode, the UTF validity check on the
replacement string was happening before the length setting when the replacement
string was zero-terminated.

68. In pcre2_substitute() in UTF mode, PCRE2_NO_UTF_CHECK can be set for the
second and subsequent calls to pcre2_match().

69. There was no check for integer overflow for a replacement group number in
pcre2_substitute(). An added check for a number greater than the largest group
number in the pattern means this is not now needed.

70. The PCRE2-specific VERSION condition didn't work correctly if only one
digit was given after the decimal point, or if more than two digits were given.
It now works with one or two digits, and gives a compile time error if more are
given.

71. In pcre2_substitute() there was the possibility of reading one code unit
beyond the end of the replacement string.

72. The code for checking a subject's UTF-32 validity for a pattern with a
lookbehind involved an out-of-bounds pointer, which could potentially cause
trouble in some environments.

73. The maximum lookbehind length was incorrectly calculated for patterns such
as /(?<=(a)(?-1))x/ which have a recursion within a backreference.

74. Give an error if a lookbehind assertion is longer than 65535 code units.

75. Give an error in pcre2_substitute() if a match ends before it starts (as a
result of the use of \K).

76. Check the length of subpattern names and the names in (*MARK:xx) etc.
dynamically to avoid the possibility of integer overflow.

77. Implement pcre2_set_max_pattern_length() so that programs can restrict the
size of patterns that they are prepared to handle.

78. (*NO_AUTO_POSSESS) was not working.

79. Adding group information caching improves the speed of compiling when
checking whether a group has a fixed length and/or could match an empty string,
especially when recursion or subroutine calls are involved. However, this
cannot be used when (?| is present in the pattern because the same number may
be used for groups of different sizes. To catch runaway patterns in this
situation, counts have been introduced to the functions that scan for empty
branches or compute fixed lengths.

80. Allow for the possibility of the size of the nest_save structure not being
a factor of the size of the compiling workspace (it currently is).

81. Check for integer overflow in minimum length calculation and cap it at
65535.

82. Small optimizations in code for finding the minimum matching length.

83. Lock out configuring for EBCDIC with non-8-bit libraries.

84. Test for error code <= 0 in regerror().

85. Check for too many replacements (more than INT_MAX) in pcre2_substitute().

86. Avoid the possibility of computing with an out-of-bounds pointer (though
not dereferencing it) while handling lookbehind assertions.

87. Failure to get memory for the match data in regcomp() is now given as a
regcomp() error instead of waiting for regexec() to pick it up.

88. In pcre2_substitute(), ensure that CRLF is not split when it is a valid
newline sequence.

89. Paranoid check in regcomp() for bad error code from pcre2_compile().

90. Run test 8 (internal offsets and code sizes) for link sizes 3 and 4 as well
as for link size 2.

91. Document that JIT has a limit on pattern size, and give more information
about JIT compile failures in pcre2test.

92. Implement PCRE2_INFO_HASBACKSLASHC.

93. Re-arrange valgrind support code in pcre2test to avoid spurious reports
with JIT (possibly caused by SSE2?).

94. Support offset_limit in JIT.

95. A sequence such as [[:punct:]b] that is, a POSIX character class followed
by a single ASCII character in a class item, was incorrectly compiled in UCP
mode. The POSIX class got lost, but only if the single character followed it.

96. [:punct:] in UCP mode was matching some characters in the range 128-255
that should not have been matched.

97. If [:^ascii:] or [:^xdigit:] are present in a non-negated class, all
characters with code points greater than 255 are in the class. When a Unicode
property was also in the class (if PCRE2_UCP is set, escapes such as \w are
turned into Unicode properties), wide characters were not correctly handled,
and could fail to match.

98. In pcre2test, make the "startoffset" modifier a synonym of "offset",
because it sets the "startoffset" parameter for pcre2_match().

99. If PCRE2_AUTO_CALLOUT was set on a pattern that had a (?# comment between
an item and its qualifier (for example, A(?#comment)?B) pcre2_compile()
misbehaved. This bug was found by the LLVM fuzzer.

100. The error for an invalid UTF pattern string always gave the code unit
offset as zero instead of where the invalidity was found.

101. Further to 97 above, negated classes such as [^[:^ascii:]\d] were also not
working correctly in UCP mode.

102. Similar to 99 above, if an isolated \E was present between an item and its
qualifier when PCRE2_AUTO_CALLOUT was set, pcre2_compile() misbehaved. This bug
was found by the LLVM fuzzer.

103. The POSIX wrapper function regexec() crashed if the option REG_STARTEND
was set when the pmatch argument was NULL. It now returns REG_INVARG.

104. Allow for up to 32-bit numbers in the ordin() function in pcre2grep.

105. An empty \Q\E sequence between an item and its qualifier caused
pcre2_compile() to misbehave when auto callouts were enabled. This bug
was found by the LLVM fuzzer.

106. If both PCRE2_ALT_VERBNAMES and PCRE2_EXTENDED were set, and a (*MARK) or
other verb "name" ended with whitespace immediately before the closing
parenthesis, pcre2_compile() misbehaved. Example: /(*:abc )/, but only when
both those options were set.

107. In a number of places pcre2_compile() was not handling NULL characters
correctly, and pcre2test with the "bincode" modifier was not always correctly
displaying fields containing NULLS:

   (a) Within /x extended #-comments
   (b) Within the "name" part of (*MARK) and other *verbs
   (c) Within the text argument of a callout

108. If a pattern that was compiled with PCRE2_EXTENDED started with white
space or a #-type comment that was followed by (?-x), which turns off
PCRE2_EXTENDED, and there was no subsequent (?x) to turn it on again,
pcre2_compile() assumed that (?-x) applied to the whole pattern and
consequently mis-compiled it. This bug was found by the LLVM fuzzer. The fix
for this bug means that a setting of any of the (?imsxU) options at the start
of a pattern is no longer transferred to the options that are returned by
PCRE2_INFO_ALLOPTIONS. In fact, this was an anachronism that should have
changed when the effects of those options were all moved to compile time.

109. An escaped closing parenthesis in the "name" part of a (*verb) when
PCRE2_ALT_VERBNAMES was set caused pcre2_compile() to malfunction. This bug
was found by the LLVM fuzzer.

110. Implemented PCRE2_SUBSTITUTE_UNSET_EMPTY, and updated pcre2test to make it
possible to test it.

111. "Harden" pcre2test against ridiculously large values in modifiers and
command line arguments.

112. Implemented PCRE2_SUBSTITUTE_UNKNOWN_UNSET and PCRE2_SUBSTITUTE_OVERFLOW_
LENGTH.

113. Fix printing of *MARK names that contain binary zeroes in pcre2test.


Version 10.20 30-June-2015
--------------------------

1. Callouts with string arguments have been added.

2. Assertion code generator in JIT has been optimized.

3. The invalid pattern (?(?C) has a missing assertion condition at the end. The
pcre2_compile() function read past the end of the input before diagnosing an
error. This bug was discovered by the LLVM fuzzer.

4. Implemented pcre2_callout_enumerate().

5. Fix JIT compilation of conditional blocks whose assertion is converted to
(*FAIL). E.g: /(?(?!))/.

6. The pattern /(?(?!)^)/ caused references to random memory. This bug was
discovered by the LLVM fuzzer.

7. The assertion (?!) is optimized to (*FAIL). This was not handled correctly
when this assertion was used as a condition, for example (?(?!)a|b). In
pcre2_match() it worked by luck; in pcre2_dfa_match() it gave an incorrect
error about an unsupported item.

8. For some types of pattern, for example /Z*(|d*){216}/, the auto-
possessification code could take exponential time to complete. A recursion
depth limit of 1000 has been imposed to limit the resources used by this
optimization. This infelicity was discovered by the LLVM fuzzer.

9. A pattern such as /(*UTF)[\S\V\H]/, which contains a negated special class
such as \S in non-UCP mode, explicit wide characters (> 255) can be ignored
because \S ensures they are all in the class. The code for doing this was
interacting badly with the code for computing the amount of space needed to
compile the pattern, leading to a buffer overflow. This bug was discovered by
the LLVM fuzzer.

10. A pattern such as /((?2)+)((?1))/ which has mutual recursion nested inside
other kinds of group caused stack overflow at compile time. This bug was
discovered by the LLVM fuzzer.

11. A pattern such as /(?1)(?#?'){8}(a)/ which had a parenthesized comment
between a subroutine call and its quantifier was incorrectly compiled, leading
to buffer overflow or other errors. This bug was discovered by the LLVM fuzzer.

12. The illegal pattern /(?(?<E>.*!.*)?)/ was not being diagnosed as missing an
assertion after (?(. The code was failing to check the character after (?(?<
for the ! or = that would indicate a lookbehind assertion. This bug was
discovered by the LLVM fuzzer.

13. A pattern such as /X((?2)()*+){2}+/ which has a possessive quantifier with
a fixed maximum following a group that contains a subroutine reference was
incorrectly compiled and could trigger buffer overflow. This bug was discovered
by the LLVM fuzzer.

14. Negative relative recursive references such as (?-7) to non-existent
subpatterns were not being diagnosed and could lead to unpredictable behaviour.
This bug was discovered by the LLVM fuzzer.

15. The bug fixed in 14 was due to an integer variable that was unsigned when
it should have been signed. Some other "int" variables, having been checked,
have either been changed to uint32_t or commented as "must be signed".

16. A mutual recursion within a lookbehind assertion such as (?<=((?2))((?1)))
caused a stack overflow instead of the diagnosis of a non-fixed length
lookbehind assertion. This bug was discovered by the LLVM fuzzer.

17. The use of \K in a positive lookbehind assertion in a non-anchored pattern
(e.g. /(?<=\Ka)/) could make pcre2grep loop.

18. There was a similar problem to 17 in pcre2test for global matches, though
the code there did catch the loop.

19. If a greedy quantified \X was preceded by \C in UTF mode (e.g. \C\X*),
and a subsequent item in the pattern caused a non-match, backtracking over the
repeated \X did not stop, but carried on past the start of the subject, causing
reference to random memory and/or a segfault. There were also some other cases
where backtracking after \C could crash. This set of bugs was discovered by the
LLVM fuzzer.

20. The function for finding the minimum length of a matching string could take
a very long time if mutual recursion was present many times in a pattern, for
example, /((?2){73}(?2))((?1))/. A better mutual recursion detection method has
been implemented. This infelicity was discovered by the LLVM fuzzer.

21. Implemented PCRE2_NEVER_BACKSLASH_C.

22. The feature for string replication in pcre2test could read from freed
memory if the replication required a buffer to be extended, and it was not
working properly in 16-bit and 32-bit modes. This issue was discovered by a
fuzzer: see http://lcamtuf.coredump.cx/afl/.

23. Added the PCRE2_ALT_CIRCUMFLEX option.

24. Adjust the treatment of \8 and \9 to be the same as the current Perl
behaviour.

25. Static linking against the PCRE2 library using the pkg-config module was
failing on missing pthread symbols.

26. If a group that contained a recursive back reference also contained a
forward reference subroutine call followed by a non-forward-reference
subroutine call, for example /.((?2)(?R)\1)()/, pcre2_compile() failed to
compile correct code, leading to undefined behaviour or an internally detected
error. This bug was discovered by the LLVM fuzzer.

27. Quantification of certain items (e.g. atomic back references) could cause
incorrect code to be compiled when recursive forward references were involved.
For example, in this pattern: /(?1)()((((((\1++))\x85)+)|))/. This bug was
discovered by the LLVM fuzzer.

28. A repeated conditional group whose condition was a reference by name caused
a buffer overflow if there was more than one group with the given name. This
bug was discovered by the LLVM fuzzer.

29. A recursive back reference by name within a group that had the same name as
another group caused a buffer overflow. For example: /(?J)(?'d'(?'d'\g{d}))/.
This bug was discovered by the LLVM fuzzer.

30. A forward reference by name to a group whose number is the same as the
current group, for example in this pattern: /(?|(\k'Pm')|(?'Pm'))/, caused a
buffer overflow at compile time. This bug was discovered by the LLVM fuzzer.

31. Fix -fsanitize=undefined warnings for left shifts of 1 by 31 (it treats 1
as an int; fixed by writing it as 1u).

32. Fix pcre2grep compile when -std=c99 is used with gcc, though it still gives
a warning for "fileno" unless -std=gnu99 us used.

33. A lookbehind assertion within a set of mutually recursive subpatterns could
provoke a buffer overflow. This bug was discovered by the LLVM fuzzer.

34. Give an error for an empty subpattern name such as (?'').

35. Make pcre2test give an error if a pattern that follows #forbud_utf contains
\P, \p, or \X.

36. The way named subpatterns are handled has been refactored. There is now a
pre-pass over the regex which does nothing other than identify named
subpatterns and count the total captures. This means that information about
named patterns is known before the rest of the compile. In particular, it means
that forward references can be checked as they are encountered. Previously, the
code for handling forward references was contorted and led to several errors in
computing the memory requirements for some patterns, leading to buffer
overflows.

37. There was no check for integer overflow in subroutine calls such as (?123).

38. The table entry for \l in EBCDIC environments was incorrect, leading to its
being treated as a literal 'l' instead of causing an error.

39. If a non-capturing group containing a conditional group that could match
an empty string was repeated, it was not identified as matching an empty string
itself. For example: /^(?:(?(1)x|)+)+$()/.

40. In an EBCDIC environment, pcretest was mishandling the escape sequences
\a and \e in test subject lines.

41. In an EBCDIC environment, \a in a pattern was converted to the ASCII
instead of the EBCDIC value.

42. The handling of \c in an EBCDIC environment has been revised so that it is
now compatible with the specification in Perl's perlebcdic page.

43. Single character repetition in JIT has been improved. 20-30% speedup
was achieved on certain patterns.

44. The EBCDIC character 0x41 is a non-breaking space, equivalent to 0xa0 in
ASCII/Unicode. This has now been added to the list of characters that are
recognized as white space in EBCDIC.

45. When PCRE2 was compiled without Unicode support, the use of \p and \P gave
an error (correctly) when used outside a class, but did not give an error
within a class.

46. \h within a class was incorrectly compiled in EBCDIC environments.

47. JIT should return with error when the compiled pattern requires
more stack space than the maximum.

48. Fixed a memory leak in pcre2grep when a locale is set.


Version 10.10 06-March-2015
---------------------------

1. When a pattern is compiled, it remembers the highest back reference so that
when matching, if the ovector is too small, extra memory can be obtained to
use instead. A conditional subpattern whose condition is a check on a capture
having happened, such as, for example in the pattern /^(?:(a)|b)(?(1)A|B)/, is
another kind of back reference, but it was not setting the highest
backreference number. This mattered only if pcre2_match() was called with an
ovector that was too small to hold the capture, and there was no other kind of
back reference (a situation which is probably quite rare). The effect of the
bug was that the condition was always treated as FALSE when the capture could
not be consulted, leading to a incorrect behaviour by pcre2_match(). This bug
has been fixed.

2. Functions for serialization and deserialization of sets of compiled patterns
have been added.

3. The value that is returned by PCRE2_INFO_SIZE has been corrected to remove
excess code units at the end of the data block that may occasionally occur if
the code for calculating the size over-estimates. This change stops the
serialization code copying uninitialized data, to which valgrind objects. The
documentation of PCRE2_INFO_SIZE was incorrect in stating that the size did not
include the general overhead. This has been corrected.

4. All code units in every slot in the table of group names are now set, again
in order to avoid accessing uninitialized data when serializing.

5. The (*NO_JIT) feature is implemented.

6. If a bug that caused pcre2_compile() to use more memory than allocated was
triggered when using valgrind, the code in (3) above passed a stupidly large
value to valgrind. This caused a crash instead of an "internal error" return.

7. A reference to a duplicated named group (either a back reference or a test
for being set in a conditional) that occurred in a part of the pattern where
PCRE2_DUPNAMES was not set caused the amount of memory needed for the pattern
to be incorrectly calculated, leading to overwriting.

8. A mutually recursive set of back references such as (\2)(\1) caused a
segfault at compile time (while trying to find the minimum matching length).
The infinite loop is now broken (with the minimum length unset, that is, zero).

9. If an assertion that was used as a condition was quantified with a minimum
of zero, matching went wrong. In particular, if the whole group had unlimited
repetition and could match an empty string, a segfault was likely. The pattern
(?(?=0)?)+ is an example that caused this. Perl allows assertions to be
quantified, but not if they are being used as conditions, so the above pattern
is faulted by Perl. PCRE2 has now been changed so that it also rejects such
patterns.

10. The error message for an invalid quantifier has been changed from "nothing
to repeat" to "quantifier does not follow a repeatable item".

11. If a bad UTF string is compiled with NO_UTF_CHECK, it may succeed, but
scanning the compiled pattern in subsequent auto-possessification can get out
of step and lead to an unknown opcode. Previously this could have caused an
infinite loop. Now it generates an "internal error" error. This is a tidyup,
not a bug fix; passing bad UTF with NO_UTF_CHECK is documented as having an
undefined outcome.

12. A UTF pattern containing a "not" match of a non-ASCII character and a
subroutine reference could loop at compile time. Example: /[^\xff]((?1))/.

13. The locale test (RunTest 3) has been upgraded. It now checks that a locale
that is found in the output of "locale -a" can actually be set by pcre2test
before it is accepted. Previously, in an environment where a locale was listed
but would not set (an example does exist), the test would "pass" without
actually doing anything. Also the fr_CA locale has been added to the list of
locales that can be used.

14. Fixed a bug in pcre2_substitute(). If a replacement string ended in a
capturing group number without parentheses, the last character was incorrectly
literally included at the end of the replacement string.

15. A possessive capturing group such as (a)*+ with a minimum repeat of zero
failed to allow the zero-repeat case if pcre2_match() was called with an
ovector too small to capture the group.

16. Improved error message in pcre2test when setting the stack size (-S) fails.

17. Fixed two bugs in CMakeLists.txt: (1) Some lines had got lost in the
transfer from PCRE1, meaning that CMake configuration failed if "build tests"
was selected. (2) The file src/pcre2_serialize.c had not been added to the list
of PCRE2 sources, which caused a failure to build pcre2test.

18. Fixed typo in pcre2_serialize.c (DECL instead of DEFN) that causes problems
only on Windows.

19. Use binary input when reading back saved serialized patterns in pcre2test.

20. Added RunTest.bat for running the tests under Windows.

21. "make distclean" was not removing config.h, a file that may be created for
use with CMake.

22. A pattern such as "((?2){0,1999}())?", which has a group containing a
forward reference repeated a large (but limited) number of times within a
repeated outer group that has a zero minimum quantifier, caused incorrect code
to be compiled, leading to the error "internal error: previously-checked
referenced subpattern not found" when an incorrect memory address was read.
This bug was reported as "heap overflow", discovered by Kai Lu of Fortinet's
FortiGuard Labs. (Added 24-March-2015: CVE-2015-2325 was given to this.)

23. A pattern such as "((?+1)(\1))/" containing a forward reference subroutine
call within a group that also contained a recursive back reference caused
incorrect code to be compiled. This bug was reported as "heap overflow",
discovered by Kai Lu of Fortinet's FortiGuard Labs. (Added 24-March-2015:
CVE-2015-2326 was given to this.)

24. Computing the size of the JIT read-only data in advance has been a source
of various issues, and new ones are still appear unfortunately. To fix
existing and future issues, size computation is eliminated from the code,
and replaced by on-demand memory allocation.

25. A pattern such as /(?i)[A-`]/, where characters in the other case are
adjacent to the end of the range, and the range contained characters with more
than one other case, caused incorrect behaviour when compiled in UTF mode. In
that example, the range a-j was left out of the class.


Version 10.00 05-January-2015
-----------------------------

Version 10.00 is the first release of PCRE2, a revised API for the PCRE
library. Changes prior to 10.00 are logged in the ChangeLog file for the old
API, up to item 20 for release 8.36.

The code of the library was heavily revised as part of the new API
implementation. Details of each and every modification were not individually
logged. In addition to the API changes, the following changes were made. They
are either new functionality, or bug fixes and other noticeable changes of
behaviour that were implemented after the code had been forked.

1. Including Unicode support at build time is now enabled by default, but it
can optionally be disabled. It is not enabled by default at run time (no
change).

2. The test program, now called pcre2test, was re-specified and almost
completely re-written. Its input is not compatible with input for pcretest.

3. Patterns may start with (*NOTEMPTY) or (*NOTEMPTY_ATSTART) to set the
PCRE2_NOTEMPTY or PCRE2_NOTEMPTY_ATSTART options for every subject line that is
matched by that pattern.

4. For the benefit of those who use PCRE2 via some other application, that is,
not writing the function calls themselves, it is possible to check the PCRE2
version by matching a pattern such as /(?(VERSION>=10)yes|no)/ against a
string such as "yesno".

5. There are case-equivalent Unicode characters whose encodings use different
numbers of code units in UTF-8. U+023A and U+2C65 are one example. (It is
theoretically possible for this to happen in UTF-16 too.) If a backreference to
a group containing one of these characters was greedily repeated, and during
the match a backtrack occurred, the subject might be backtracked by the wrong
number of code units. For example, if /^(\x{23a})\1*(.)/ is matched caselessly
(and in UTF-8 mode) against "\x{23a}\x{2c65}\x{2c65}\x{2c65}", group 2 should
capture the final character, which is the three bytes E2, B1, and A5 in UTF-8.
Incorrect backtracking meant that group 2 captured only the last two bytes.
This bug has been fixed; the new code is slower, but it is used only when the
strings matched by the repetition are not all the same length.

6. A pattern such as /()a/ was not setting the "first character must be 'a'"
information. This applied to any pattern with a group that matched no
characters, for example: /(?:(?=.)|(?<!x))a/.

7. When an (*ACCEPT) is triggered inside capturing parentheses, it arranges for
those parentheses to be closed with whatever has been captured so far. However,
it was failing to mark any other groups between the highest capture so far and
the currrent group as "unset". Thus, the ovector for those groups contained
whatever was previously there. An example is the pattern /(x)|((*ACCEPT))/ when
matched against "abcd".

8. The pcre2_substitute() function has been implemented.

9. If an assertion used as a condition was quantified with a minimum of zero
(an odd thing to do, but it happened), SIGSEGV or other misbehaviour could
occur.

10. The PCRE2_NO_DOTSTAR_ANCHOR option has been implemented.

****

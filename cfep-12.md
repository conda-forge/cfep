
<table>
<tr><td> Title </td><td> Removing packages that violate the terms of the source package</td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Isuru Fernando &lt;isuruf@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Feb 26, 2020</td></tr>
<tr><td> Updated </td><td> Feb 26, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Source packages that we use to create conda packages have terms and conditions that allow
us to redistribute the package in binary form. This CFEP codifies the conduct when these
violations are reported.

## Implementation

When a violation is brought to the attention of the Core team, the feedstock maintenance
team should be made aware by the Core team in a github issue on the feedstock and notified using the
github handles of the maintenance team members.

A week would be given to the maintenance team to respond to the claim and
another week would be given to address the issue. The feedstock maintenance team
may ask to extend the deadlines.

If the issue is not addressed after the specified time period, the package will be moved
to the broken label.

We suggest a message to the maintainers along the following lines

    Hi! We (`@conda-forge/core`) have noticed that this package does not have
    the license file required for us to be able to distribute this code. Nearly every
    open-source license has such a requirement. It is extremely important that we
    at `conda-forge` respect license restrictions and requirements such as this one
    so that we can be good stewards of the open-source community. 
    
    We are requesting that you add the correct license file to this feedstock. 
    If this is not done within two weeks, the packages from this feedstock be 
    moved to the broken label. If you'd like to extend this deadline or need 
    to discuss something with us, please feel free to respond to this issue.
    
    We also very much appreciate the time you have taken to maintain this 
    feedstock and your understanding on this issue!
## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).

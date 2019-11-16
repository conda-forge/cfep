
<table>
<tr><td> Title </td><td> Simple staged-recipes Automation </td>
<tr><td> Status </td><td> Draft  </td></tr>
<tr><td> Author(s) </td><td> Matthew R Becker &lt;becker.mr@gmail.com&gt; C.J. Wright &lt;&gt;</td></tr>
<tr><td> Created </td><td> Nov 15, 2019</td></tr>
<tr><td> Updated </td><td> Nov 15, 2019</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

While we are still debating CFEP-06 (https://github.com/conda-forge/conda-forge-enhancement-proposals/pull/9), we can 
implement a simple bot to close excessively old PRs on staged recipes. Our proposal is to use the GitHub stale bot to 
close PRs on staged-recipes that are more than 6 months old. The staged-recipes team is charged with both maintaining 
this bot and deciding on a nice and friendly message. 

## Motivation

As of writing, the staged recipes repo has 289 PRs open from 2018 or earlier. Over time, the number of open PRs on 
staged recipes has grown as PRs are abandoned but not closed. The staged recipes team is busy responding to new PRs 
and so has not had time to clean out the old PRs. This CFEP adds a minimal level of automation in order to help the 
team manage the growing number of open PRs.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).

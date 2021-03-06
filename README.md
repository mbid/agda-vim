Version 0.1.0

A simple vim mode for interactively editing Agda code.

For a demonstration see:

[![agda-vim Introduction on YouTube](http://img.youtube.com/vi/i7Btts-duZw/0.jpg)](https://www.youtube.com/watch?v=i7Btts-duZw)

It incorporates the syntax and unicode input files from <http://wiki.portal.chalmers.se/agda/pmwiki.php?n=Main.VIMEditing>
but extends them with support for interacting with the Agda process in the same manner as the emacs mode.  In addition,
it sets Agda as the make program to automatically generate and load the local vim highlighting files and to jump
to errors.  The make program is currently just "agda --vim", i.e. it does not compile the program.

This requires syntax highlighting to be turned on and, for the interactivity, python plugins to be supported.  This
interacts via the same interface emacs uses, though a lot of the logic is in the emacs mode.  This is not an officially
supported mode, so there's no guarantee it will work with different versions of Agda.  I've currently used it with
Agda 2.3.2.1 through 2.4.2.2.  I have not tested Literate Agda files at all and would be a bit surprised if they worked.

This currently does not use any of the vim "package" formats.  Copying the files into your .vim folder
and adding the following line to filetypes.vim should be adequate:

    au BufNewFile,BufRead *.agda setf agda

You can add paths to the paths Agda searches by setting `g:agda_extraincpaths` as demonstrated by the command below
which could be added to your .vimrc.

    let g:agda_extraincpaths = ["/home/derek/haskell/agda-stdlib-0.8.1/src"]

In the commands below, the `<LocalLeader>` is set by setting `maplocalleader` which needs to occur before the mappings
are made.

    let maplocalleader = ","

The commands and mappings as defined currently are below:

    command! -nargs=0 Load call Load(0)
    command! -nargs=0 Reload silent! make!|redraw!
    command! -nargs=0 RestartAgda python RestartAgda()
    command! -nargs=0 ShowImplicitArguments python sendCommand('ShowImplicitArgs True')
    command! -nargs=0 HideImplicitArguments python sendCommand('ShowImplicitArgs False')
    command! -nargs=0 ToggleImplicitArguments python sendCommand('ToggleImplicitArgs')
    command! -nargs=0 Constraints python sendCommand('Cmd_constraints')
    command! -nargs=0 Metas python sendCommand('Cmd_metas')
    command! -nargs=0 SolveAll python sendCommand('Cmd_solveAll')
    command! -nargs=1 ShowModule python sendCommand('Cmd_show_module_contents_toplevel "%s"' % "<args>")
    command! -nargs=1 SetRewriteMode python setRewriteMode("<args>")

    nmap <buffer> <LocalLeader>l :Reload<CR>
    nmap <buffer> <LocalLeader>t :call Infer()<CR>
    nmap <buffer> <LocalLeader>r :call Refine("False")<CR>
    nmap <buffer> <LocalLeader>R :call Refine("True")<CR>
    nmap <buffer> <LocalLeader>g :call Give()<CR>
    nmap <buffer> <LocalLeader>c :call MakeCase()<CR>
    nmap <buffer> <LocalLeader>a :call Auto()<CR>
    nmap <buffer> <LocalLeader>e :call Context()<CR>
    nmap <buffer> <LocalLeader>n :call Normalize("False")<CR>
    nmap <buffer> <LocalLeader>N :call Normalize("True")<CR>
    nmap <buffer> <LocalLeader>M :call ShowModule()<CR>
    nmap <buffer> <LocalLeader>m :Metas<CR>

    " Show/reload metas
    nmap <buffer> <C-e> :Metas<CR>
    imap <buffer> <C-e> <C-o>:Metas<CR>

    " Go to next/previous meta
    nmap <buffer> <silent> <C-g>  :let _s=@/<CR>/ {!\\| ?<CR>:let @/=_s<CR>2l
    imap <buffer> <silent> <C-g>  <C-o>:let _s=@/<CR><C-o>/ {!\\| ?<CR><C-o>:let @/=_s<CR><C-o>2l

    nmap <buffer> <silent> <C-y>  2h:let _s=@/<CR>? {!\\| \?<CR>:let @/=_s<CR>2l
    imap <buffer> <silent> <C-y>  <C-o>2h<C-o>:let _s=@/<CR><C-o>? {!\\| \?<CR><C-o>:let @/=_s<CR><C-o>2l

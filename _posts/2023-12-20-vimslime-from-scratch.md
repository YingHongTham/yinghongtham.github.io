---
layout: post
title:  "Writing Vim Slime for Julia"
published: true
---

Lately I've been writing Julia for some projects,
and since it has an interactive mode,
I like to write code in a file, then copy it out to the REPL to execute.
It gets a bit tiresome to have to highlight, copy, switch terminals,
paste, then switch back. Of course someone else has encountered
this same problem before, and wrote some scripts to help with it.
I first learned of this possibility with Emacs SLIME mode,
and later I found this vim plugin [vim-slime](
https://github.com/jpalardy/vim-slime/tree/main),
though it didn't have a ftplugin for Julia.
I decided to try to roll my own version,
to get some more experience working with terminals and buffers,
inspired by my little adventure with Build-Your-Own-Editor.

Initially I attempted to just `echo` out the yanked text
and pipe it to the Julia REPL process, but for some reason
it didn't work, it seemed like it was piping the text to its
parent terminal process...
Then I tried tmux, which is used in vim-slime,
but I couldn't figure out how to send text to other panes...
Then I followed [original version of vim-slime](
https://technotales.wordpress.com/2007/10/03/like-slime-for-vim/)
which used `screen`, but ultimately it could not handle many lines...
Eventually I found `tmux`'s functionalities for working with buffers,
and got it to work!

`~/.config/nvim/ftplugin/julia.vim`:

```
function Send_Clipboard_to_Pane()
	if !exists("g:julia_pane")
		call Set_Julia_Pane_Prompt()
	end

	" create a buffer in tmux called x-clip and copy contents from clipboard
	call system("tmux set-buffer -b x-clip \"$(xclip -o -selection clipboard)\"")
	call system("tmux paste-buffer -b x-clip -t " . g:julia_pane)
	call system("tmux send-keys -t " . g:julia_pane . " 'Enter'")
endfunction
 
function Set_Julia_Pane_Prompt()
	if !exists("g:julia_pane")
		" suggested default value; see pane number with $ tmux list-pane
		let g:julia_pane = "%1"
	end

	let g:julia_pane = input("tmux pane: ", g:julia_pane)
endfunction
		
" yank current function into clipboard (register "+)
function YankJuliaFunction()
	" save current cursor position
	let save_cursor = getcurpos()
	let save_winview = winsaveview()

	" search for the latest function above cursor, then delete search record
	call search("function", "b")
	call histdel("search", -1)

	" use % to move to end; default julia.vim already handles with matchit
	execute "norm 0V%"
	redraw
	sleep 50ms
	execute "norm \"+y"

	" return cursor to start position
	call setpos('.', save_cursor)
	call winrestview(save_winview)
endfunction

" yank current block into clipboard (register "+)
" blocks are delineated with markers #++#
function YankJuliaBlock()
	" save current cursor position
	let save_cursor = getcurpos()
	let save_winview = winsaveview()

	if search("#++#", "bW") == 0
		" failed to find marker, go to top of file
		execute "norm gg"
	end
	call histdel("search", -1)
	execute "norm 0Vj"
	if search("#++#", "W") == 0
		execute "norm G"
	end
	redraw
	sleep 50ms
	call histdel("search", -1)
	execute "norm \"+y"

	" return cursor to start position
	call setpos('.', save_cursor)
	call winrestview(save_winview)
endfunction

nmap <F6> :call YankJuliaFunction()<CR>
nmap <F7> :call YankJuliaBlock()<CR>
nmap <F9> :call Send_Clipboard_to_Pane()<CR>
```

I also have the following key binding in general in `~/.config/nvim/init.vim`:
```
"send to clipboard selection
vmap <F8> "+y
nmap <F8> :let @+=@"<CR>
```




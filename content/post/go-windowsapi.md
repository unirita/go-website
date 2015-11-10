+++
date = "2015-11-10T11:07:06+09:00"
draft = true
title = "WindowsAPIを呼ぶ"
author = "shanxia"
categories = ["並列処理"]
+++

# Go言語からWindowsAPIを呼ぶ
Go言語には、標準で多くのシステムコールが持つ機能が実装されていますが、それでも足りない場合があります。特にWindowsは、LinuxやMacOSXに比べて、不足していると感じる場合があります。
このような場合、直接自分でライブラリを読み込んで、システムコールを呼ぶ機能がGo言語には備わっています。
これはGoの標準パッケージでも行っている方法なので、syscallパッケージ内のソースコードを見るのが一番ですが、ここで簡単に説明します。

## WindowsAPIを呼ぶ
Goの標準パッケージ「syscall/zsyscall_windows.go」では、かなりの数のWindowsAPIがあらかじめ読み込まれています。ここで定義されていないWindowsAPIを使いたい場合の方法を説明します。

WindowsAPIを呼ぶには、syscallパッケージを利用すると簡単に行うことができます。使いたいAPIが定義されたDLLをロードし、そこからAPIのファンクションポインタを取得します。

次のサンプルプログラムでは、GetModuleFileName()を使用して、カレントパスを取得しています。

```go
package mainimport (	"fmt"	"syscall"	"unsafe")type userDll struct {	dll *syscall.DLL}var (	kernel32_dll           = loadDLL("kernel32.dll")
	// ASCII版のAPI	procGetModuleFileName  = kernel32_dll.findProc("GetModuleFileNameA")	// Unicode版のAPI	procGetModuleFileNameW = kernel32_dll.findProc("GetModuleFileNameW"))// nameに指定されたDLLファイルをloadしますfunc loadDLL(name string) *userDll {	dll, err := syscall.LoadDLL(name)	if err != nil {		panic(err)	}	return &userDll{dll}}// nameで指定された、dll内のファンクションポインタを取得しますfunc (c *userDll) findProc(name string) *syscall.Proc {	proc, err := c.dll.FindProc(name)	if err != nil {		panic(err)	}	return proc}func main() {	const max_path int = 520	// Ascii版の場合	fmt.Println("Ascii版:")	// カレントパスを保持するbyteスライス	var bufA [max_path]byte	// GetModuleFileName()を呼ぶ	// 下の場合は、GetModuleFileName( NULL, &buf, 520 ) という引数で呼んでいる	r1, r2, lastErr := procGetModuleFileName.Call(0, uintptr(unsafe.Pointer(&bufA)), (uintptr)(max_path))	fmt.Printf("r1 = %v, r2 = %v, lastErr = %v\n", r1, r2, lastErr)	// r1には、bufA へコピーされた文字列の長さが返されるので、その部分だけを出力する	fmt.Printf("[%s]\n", bufA[:r1])	// Unicode版の場合	fmt.Println("\nUnicode版:")	// カレントパスを保持するbyteスライス	var bufW [max_path]byte	// GetModuleFileNameW()を呼ぶ	// 引数指定は上と同じ	r1, r2, lastErr = procGetModuleFileNameW.Call(0, uintptr(unsafe.Pointer(&bufW)), (uintptr)(max_path))	fmt.Printf("r1 = %v, r2 = %v, lastErr = %v\n", r1, r2, lastErr)	// LPWSTR　型にはUTF-16のコードで返されるため、Goのstring型へ変換する	s := syscall.UTF16ToString((*[max_path]uint16)(unsafe.Pointer(&bufW[0]))[:r1])	fmt.Printf("%s\n", s)}```
これを実行すると、次の様な結果が返ります。

<pre class="output">
Ascii版:r1 = 18, r2 = 0, lastErr = The operation completed successfully.[C:\sample\main.exe]Unicode版:r1 = 18, r2 = 0, lastErr = The operation completed successfully.[C:\sample\main.exe]
</pre>

全角文字が含まれるフォルダで実行した場合は、Ascii版の出力が文字化けします。
気をつけるべき点として、APIを呼んでから戻される3つめの戻り値は、成功した場合にも文字列が返ってくるため、[if err != nil ]でのエラーチェックを行ってはいけません。

また、WindowsAPIのインタフェースと、Go言語で使用する型は異なるため、たとえ同じint型でもそのまま渡すことができません。
サンプルのように、特別に用意された uintptr型にキャストして渡したり、ポインタ型の場合は、unsafe.Pointerを使用する必要があります。


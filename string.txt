; Загружает строку из файла и возвращает ее указатель и размер
load_string:
    push bp
    mov bp, sp
    mov ah, 0x3D
    mov al, 0x00
    lea dx, [bp+4]
    int 0x21
    mov bx, ax
    mov ah, 0x3F
    mov cx, 0xFFFF
    lea dx, [bp-2]
    int 0x21
    mov [bx], dx
    mov [bx+2], cx
    pop bp
    ret


; Подсчитывает сумму строки и выводит ее на экран
sumstring:
    push bp
    mov bp, sp
    mov cx, [bp+4]
    mov si, [bp+6]
    xor bx, bx
sumloop:
    mov al, [si]
    add bx, ax
    inc si
    loop sumloop
    mov ah, 0x0E
    mov al, '-'
    int 0x10
    mov al, ' '
    int 0x10
    mov ax, bx
    call printnum
    mov ah, 0x0D
    int 0x10
    mov ah, 0x0A
    int 0x10
    pop bp
    ret


; Сравнивает две строки и выводит результат на экран
comparestrings:
    push bp
    mov bp, sp
    mov cx, [bp+4]
    mov si, [bp+6]
    mov dx, [bp+10]
    mov di, [bp+12]
    cmp cx, [bp+8]
    jne .notequal
    cld
    rep cmpsb
    je .equal
.not_equal:
    mov ah, 0x0E
    mov al, 'N'
    int 0x10
    mov al, 'O'
    int 0x10
    jmp .end
.equal:
    mov ah, 0x0E
    mov al, 'E'
    int 0x10
    mov al, 'Q'
    int 0x10
.end:
    pop bp
    ret


; Выводит меню и возвращает выбранный пункт
menu:
    push bp
    mov bp, sp
    mov ah, 0x0
    int 0x16
    cmp al, '1'
    je .loadfile
    cmp al, '2'
    je .printstrings
    cmp al, '3'
    je .loadeditedfile
    cmp al, '4'
    je .comparestrings
    cmp al, '5'
    je .writeresult
    cmp al, '6'
    je .exit
    jmp menu
.loadfile:
    call loadfile
    jmp menu
.printstrings:
    call printstrings
    jmp menu
.loadeditedfile:
    call loadeditedfile
    jmp menu
.comparestrings:
    call comparestringsall
    jmp menu
.writeresult:
    call write_result
    jmp menu
.exit:
    mov ax, 0x4C00
    int 0x21
    pop bp
    ret


; Записывает результат в файл и выводит его на экран
write_result:
    push bp
    mov bp, sp
    mov ah, 0x3C
    lea dx, [bp+4]
    mov cx, 0x0005
    int 0x21
    mov bx, ax
    mov ah, 0x40
    mov cx, [bp+6]
    mov dx, [bp+8]
    int 0x21
    mov ah, 0x3E
    mov cx, [bp+6]
    mov dx, [bp+8]
    int 0x21
    mov ah, 0x3F
    mov cx, 0xFFFF
    lea dx, [bp-2]
    int 0x21
    mov ah, 0x09
    lea dx, [bp-2]
    int 0x21
    pop bp
    ret

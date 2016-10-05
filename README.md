# ASM
#### Краткое содержание [статей Рикардо Нарваха](http://pro.dtn.ru/cr.html)
00000000 (hex) = 0 (dec)	
00000000 -> 7FFFFFFF (hex) = 0 -> 2147483647 (dec) -- положительные числа		
80000000 -> FFFFFFFF (hex) = -2147483647 -> 0 (dec) -- отрицательные числа	
	
Стек - область памяти, в которой сохраняются данные, которые должны быть восстановлены в ближайшее время. Подробнее об организации памяти, стеке, куче и др [здесь](https://habrahabr.ru/post/128991/)	
	
Регистры:	
* ESP - указывает на верхнее значение стека
* EIP - указывает на инструкцию, выполняющуюся в данный мамент
* EAX, EBX, ECX, EDX, EBP, ESI, EDI, а также ESP, EIP - 32х битные регистры
* Для EAX, EBX, ECX, EDX можно взять вторые 16бит информации (AX, BX, CX, DX соответственно), которые деляться на (AH и AL, BH и BL и тд)
* Для EBP, ESI, EDI, ESP можно получить только вторую половину аналогично	
	
Флаги:	
* O - флаг переполнения. Активируется, когда в результате операции изменяется знак и возвращается неправильное значение
* А - дополнительный знак переноса
* Р - флаг четности. Активируется, когда результат выполнения операции содержит четное число единиц в двоичной СС (1010, 1100, 11 и тд)
* Z - флаг нуля. Активируется, когда результат выполнения операции равняется нулю
* S - флаг знака. Активируется, когда результат выполнения операции отрицателен
* С - флаг переноса. Активируется, когда превышается максимальное значение, которое может содержать регистр(например, если в EAX поместим FFFFFFFF  и выполним ADD, 1)	
	
Иструкции для работы со стеком и перемещением данных:		
* NOP (no operation) - операция, которая не производит никакиз изменений в регистрах, стеке или памяти
* PUSH - помещает значение в стек:	
** PUSH 123 помещает число; PUSH EAX помещает регистр; PUSH [12345678] помещает оласть значение, находящееся по адресу памяти**
* POP - достает первое значение из стека и помещает его в указанное параметром место (POP EAX)
* PUSHAD - помещает значение регистров в стек в определенном порядке (эквивалентно PUSH EAX, ECX, EDX, EBX, ESP, EBP, ESI, EDI)
* POPAD - перемещает из стека значения в соответствующие регистры (эквивалентно POP EDI, ESI, EBP, ESP, EBX, EDX, ECX, EAX)
* PUSHA, POPA аналогично предыдущим, только перемещаются в стек и берутся из стека 16битные значения (AX, CX, DC, BX и т.д.). ESP и SP исключаются
* MOV - перемещает второй операнд в первый (MOV EAX, EBX - перемещается значение из EBX в EAX)
* MOVSX(Move with Sign-Extension) - Копирует содержимое второго операнда, который может быть регистром или адресом памяти, в первый (который должен быть в два раза больше, чем второй), заполняя остальные биты слева значением самого значимого бита второго операнда. Если второе число положительное, то левая часть первого числа заполняется нулями, если отрицательное, то FFFF
* MOVZX (Move with Zero-Extension) - Аналогично с предыдущим, но в начале вставляет нули
* LEA(Load Effective Address) - Похожа на инструкцию MOV, но первый операнд - это регистр общего назначения, а второй - адрес ячейки памяти. Эта инструкция полезна в первую очередь тогда, когда адрес зависит от предыдущих вычислений.
* XCHG (Exchange Rwgistr/Memory with Register) - обменивает содержимое двух операндов (XCHG EAX, EBX)
	
Математические инструкции:	
* INC, DEC - инкремент. декремент. Можно работать с регистрами, ячейками памяти
* ADD - складывает два операнда и сохраяет результат в первом. Также можно складывать регистр с содержимым ячейки памяти
* ADC(ADD с переносом) - складывает дв операнда и флаг переноса (флаг C) и сохраняет результат в первом операнде.
* SUB - вычисляет остаток от вычитания второго операнда из первого и сохраняет результат в последнем
* SBB - вычисляет разность между операндами, вычитает флаг переноста от того, что получилось и сохраняет результат в первый операнд
* MUL - инструкция умножения. Принимет один операнд и сохраняте резултат в EDX:EAX(сохраняет в EAX и, если происходит переполнение регистра, сохраняет оставшуюся часть в EDX). Выполняет умножение буз учета знаков подаваемого операнда на EAX.
* IMUL - аналогично предыдущему пункту, но учитывает знак. Также можно передавать источники и приемники, вплоть до 3хоперандов. Первый - место, куда помещается результать и который должен всегда быть регистром, второй и третий - два значения, которые нужно перемножить. Если первый операнд переполняется, то в нем остается только то, тчо поместилось, остальное отбрасывается.
* DIV, IDIV - инструкции, обратные MUL и IMUL. DIV имеет только один операнд, не учитывает знаки и помещает результат в EDX:EAX. IDIV всегда учитывает знаки. Если используется один операнд, то, как и DIV, сохраняет результат в EDX:EAX, в случае двух - делит один на другой и сохраняет результат в первый, а при трёх операндах делит второй на третий и сохраняет то, что получилось, в первый. 
* XADD(обменять и сложить) - сначала обменивает значения операндов, затем складывает их и помещает сумму в первый операнд
* NEG - изенение знака операнда на противоположный	
	
	
Логические (побитовые) инструкции(принимают два операнда и резутьтат записывают в первый):
* AND, OR, XOR, NOT	
	
		
		
DWORD - 4 байта, WORD - 2 байта, BYTE - 1 байт



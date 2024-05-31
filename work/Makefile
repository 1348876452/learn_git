CROSS_COMPILE = riscv64-unknown-elf-

QEMU = qemu-system-riscv64
QFLAGS = -nographic -smp 1 -machine spike -serial pty 

LLVM_OPTS = -Wall -mno-relax  -static -nostdlib -nostartfiles -fno-common -fno-builtin-printf
RISCV_OPTS = -march=rv64imafdc -mabi=lp64 -Tenv/riscv_link.ld


CC      :=${CROSS_COMPILE}gcc
AS      :=$(CROSS_COMPILE)as
LINK    :=$(CROSS_COMPILE)ld
OBJDUMP :=$(CROSS_COMPILE)objdump
OBJCOPY :=$(CROSS_COMPILE)objcopy
GDB     :=$(CROSS_COMPILE)gdb

sboom=~/chipyard/sims/verilator/simulator-chipyard-SmallBoomConfig
mboom=~/chipyard/sims/verilator/simulator-chipyard-MediumBoomConfig
lboom=~/chipyard/sims/verilator/simulator-chipyard-LargeBoomConfig
DIR_INCLUDE=../include/
DIR_SRC=../src/
DIR_RISCV=../riscv/
DIR_OBJ=../obj/
debug_name=../riscv/$(name)
name=



CFLAGS  :=-fno-common -fno-builtin-printf -specs=htif_nano.specs -c -g
CFLAGS_O:= -static -specs=htif_nano.specs
VPATH=../src/ : ../include/: ../riscv


program_c=$(wildcard ../src/*.c)
program_s=$(wildcard ../src/*.s)
program_S=$(wildcard ../src/*.S)
riscv=$(wildcard *.riscv)
src=$(patsubst ../src/%,%,$(program_c))
obj=$(name:%.c=%.o)


$(name).riscv:$(DIR_OBJ)$(name).o
	${CC} ${CFLAGS_O} $< -o $(name).riscv
$(DIR_OBJ)%.o:$(DIR_SRC)%.c
	$(CC) $(CFLAGS) $< -o $@ -I $(DIR_INCLUDE)
$(DIR_OBJ)%.o:$(DIR_SRC)%.s
	$(CC) $(CFLAGS) $< -o $@ -I $(DIR_INCLUDE)
$(DIR_OBJ)%.o:$(DIR_SRC)%.S
	$(CC) $(LLVM_OPTS) $(RISCV_OPTS) $< -o $@ -I $(DIR_INCLUDE)


.PHONY:printf clean qemu gdb objdump smallboom mediumboom largeboom

printf:
	@echo ''
	@echo '>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>使用方法<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<'
	@echo ''
	@echo '所有的c文件：$(patsubst ../src/%,%,$(program_c))'
	@echo '所有的s文件：$(patsubst ../src/%,%,$(program_s)) $(patsubst ../src/%,%,$(program_S))'
	@echo
	@echo
	@echo '一、 生成.riscv可执行文件的方法'
	@echo '     举例  make name=spectre-v1'
	@echo
	@echo
	@echo     name可选的参数有：   $(patsubst %.c,%,${src})  $(patsubst ../src/%.s,%,$(program_s)) $(patsubst ../src/%.S,%,$(program_S))
	@echo ''
	@echo ''
	@echo '二、 使用qemu+gdb调试'
	@echo '    1、 打开qemu：make qemu name= '
	@echo ''
	@echo '    2、 打开gdb：make gdb name= '
	@echo ''
	@echo '      ' name可选的参数有： $(riscv)
	@echo
	@echo '三、使用boom仿真。Makefile已声明过路径的boom有smallboom、mediumboom、largeboom'
	@echo '    举例：make smallboom name= '
	@echo ''
	@echo '>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<'
	@echo ''	
qemu:
	$(QEMU) $(QFLAGS) -kernel $(name) -S -gdb tcp::334455

gdb:
	$(GDB) $(name) -ex 'target remote localhost:334455' -x ./gdbinit

objdump:
	$(OBJDUMP) -S -G -l -m riscv $(name).riscv
smallboom:
	$(sboom) $(name)
mediumboom:
	$(mboom) $(name)
largeboom:
	$(lboom) $(name)
clean:
	rm -f ../obj/*.o *.riscv 



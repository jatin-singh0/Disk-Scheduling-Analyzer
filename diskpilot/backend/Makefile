CC     ?= gcc
CFLAGS  = -std=c11 -Wall -Wextra -O2 -D_DEFAULT_SOURCE
SRC     = algorithms.c wasm_api.c

all: diskpilot_cli libdiskpilot.so

diskpilot_cli: $(SRC) main.c
	$(CC) $(CFLAGS) $(SRC) main.c -o $@

libdiskpilot.so: $(SRC)
	$(CC) $(CFLAGS) -fPIC -shared $(SRC) -o $@

native: diskpilot_cli
	./diskpilot_cli --demo

test: test_algorithms
	./test_algorithms

test_algorithms: $(SRC) test_algorithms.c
	$(CC) $(CFLAGS) $(SRC) test_algorithms.c -o $@

# Optional: needs Emscripten (emcc). Not required for the normal run.
wasm:
	emcc -std=c11 -O2 algorithms.c wasm_api.c -o ../frontend/diskpilot.js -s MODULARIZE=1 -s EXPORT_NAME=DiskPilotModule \
	  -s EXPORTED_FUNCTIONS=_diskpilot_run,_diskpilot_free -s EXPORTED_RUNTIME_METHODS=ccall,UTF8ToString

clean:
	rm -f diskpilot_cli libdiskpilot.so test_algorithms

.PHONY: all native test wasm clean

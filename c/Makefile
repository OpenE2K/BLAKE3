CC=lcc
CFLAGS=-O3 -ffast -Wall -Wextra -std=c11 -pedantic -fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE -fvisibility=hidden -DBLAKE3_TESTING -DBLAKE3_NO_AVX512
LDFLAGS=-pie -Wl,-z,relro,-z,now
EXTRAFLAGS=-Wa,--noexecstack

AVX2CFLAGS=-DBLAKE3_NO_SSE41 -DBLAKE3_NO_SSE2
SSE41CFLAGS=-DBLAKE3_NO_SSE2 -DBLAKE3_NO_AVX2
SSE2CFLAGS=-DBLAKE3_NO_SSE41 -DBLAKE3_NO_AVX2

blake3_avx2: blake3.c blake3_portable.c example.c blake3_avx2.o blake3_avx2_d.o
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(AVX2CFLAGS) $^ -o $@ $(LDFLAGS)

blake3_sse41: blake3.c blake3_portable.c example.c blake3_sse41.o blake3_sse41_d.o
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE41CFLAGS) $^ -o $@ $(LDFLAGS)

blake3_sse2: blake3.c blake3_portable.c example.c blake3_sse2.o blake3_sse2_d.o
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE2CFLAGS) $^ -o $@ $(LDFLAGS)

blake3_port: blake3.c blake3_dispatch.c blake3_portable.c example.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) -DBLAKE3_NO_SSE2 -DBLAKE3_NO_SSE41 -DBLAKE3_NO_AVX2 $^ -o $@ $(LDFLAGS)

blake3_avx2_d.o: blake3_dispatch.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(AVX2CFLAGS) -c $^ -o $@

blake3_sse41_d.o: blake3_dispatch.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE41CFLAGS) -c $^ -o $@

blake3_sse2_d.o: blake3_dispatch.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE2CFLAGS) -c $^ -o $@

blake3_sse2.o: blake3_sse2.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE2CFLAGS) -c $^ -o $@ -msse2

blake3_sse41.o: blake3_sse41.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(SSE41CFLAGS) -c $^ -o $@ -msse4.1

blake3_avx2.o: blake3_avx2.c
	$(CC) $(CFLAGS) $(EXTRAFLAGS) $(AVX2CFLAGS) -c $^ -o $@ -mavx2

all: blake3_avx2 blake3_sse41 blake3_sse2 blake3_port

bench: blake3_avx2 blake3_sse41 blake3_sse2 blake3_port
	time ./blake3_avx2 < file
	time ./blake3_sse41 < file
	time ./blake3_sse2 < file
	time ./blake3_port < file
	time md5sum < file
	time sha256sum < file
	time sha512sum < file

clean: 
	rm -f *.o blake3_avx2 blake3_sse41 blake3_sse2 blake3_port

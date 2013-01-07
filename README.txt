This is openssl-1.0.0g for the Marmalade SDK.

Marmalade ships with cyassl, which has an openssl compatibility layer. However, cyassl is optimised for resource-constrained platforms, and so lacks a large portion of the functionality provided by openssl.

This port allows you to build openssl as a static library for linking with your projects. Open the openssl.mkb file to build the library for each of the standard configurations (X86/GCC ARM Debug/Release). Note that, in contrast to the standard distribution of openssl, this port produces a single library - openssl.lib or openssl_d.lib  - as its output. There is no separate libcrypto.

To use the libary, you can add its path to the options section of your project's mkb file. For example, if your directory structure is as follows:

common_parent
  ->openssl_marmalade
  ->your_project
  
then add the following to your mkb:
 
options
{
  module_path_append="../openssl_marmalade"
}

Then add an entry to the subprojects section of your mkb:

subprojects
{
  openssl
}

Make sure that iwssl is not specified in the subprojects section!

Caveats:

1. Not all openssl functionality has been included by default. If something you need is missing, let me know, and I'll add it.

2. Compared to cyassl, openssl is BIG - depending on what functionality you use, it can add anywhere from 500K - 3MB+ to your app.

3. openssl requires its pseudo-random number generator to be seeded with sufficient entropy. Failure to seed the PRNG properly will result in errors. On unix-style systems, openssl typically makes use of either (a) the /dev/random device or (b) an entropy-gathering daemon to seed the PRNG. Neither of these is available with Marmalade. As a substitute, you can use s3eCrypto to seed the PRNG, similar to the following:

	#include "rand.h"
	#include "s3eCrypto.h"
	
	... 
	
	for ( int i = 0; i < 256; i++ )
	{
		int32 r = s3eCryptoRand();
		RAND_add(&r, sizeof(r), sizeof(r));
	}
	
Note that the Marmalade SDK docs explicitly point out that no guarantee is made as to how random the values returned by s3eCryptoRand will be. The security of openssl's encryption is only as good as the "pure" randomness of the PRNG's seed, so if you know of a better way than the above, by all means use it!

4. To reduce clutter, all of the openssl test code has been removed, along with other support files that are not necessary to build the static lib. These can be found in the original source tarball in the distrib directory. 

Any suggestions/bug reports/requests - please email:
muppetlabs[at]yahoo.com

Credits:

Thanks to Sébastien Côté for his patch to fix incorrect definitions of readsocket/writesocket in eos.h.





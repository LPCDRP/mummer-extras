
TESTS = snps2vcf.test

AM_TESTS_ENVIRONMENT = PATH="$(top_srcdir)/src:$$PATH"; VPATH="$(top_srcdir)/test/data:$VPATH"; export PATH VPATH;

# http://gnu-automake.7480.n7.nabble.com/DELETE-ON-ERROR-test-suite-log-and-PRECIOUS-td20856.html
.PRECIOUS: $(TEST_SUITE_LOG)

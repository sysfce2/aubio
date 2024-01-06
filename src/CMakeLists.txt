add_library (aubio SHARED)

set_target_properties (aubio PROPERTIES SOVERSION 5.4.8)

# check for headers
# https://cmake.org/cmake/help/latest/module/CheckIncludeFile.html
include (CheckIncludeFile)
check_include_file ("stdlib.h" HAVE_STDLIB_H)
check_include_file ("stdio.h" HAVE_STDIO_H)
# TODO complex / fftw
#check_include_file ("complex.h" HAVE_COMPLEX_H)
#check_include_file ("fftw3.h" HAVE_FFTW3)
check_include_file ("math.h" HAVE_MATH_H)
check_include_file ("string.h" HAVE_STRING_H)
check_include_file ("errno.h" HAVE_ERRNO_H)
check_include_file ("limits.h" HAVE_LIMITS_H)
check_include_file ("stdarg.h" HAVE_STDARG_H)
# for examples/
check_include_file ("unistd.h" HAVE_UNISTD_H)
check_include_file ("getopt.h" HAVE_GETOPT_H)

# check for optional dependencies
# https://cmake.org/cmake/help/latest/command/find_package.html#command:find_package
# TODO make pkg-config optional
find_package (PkgConfig REQUIRED)

pkg_check_modules (SNDFILE sndfile>=1.0.4)
if (SNDFILE_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_SNDFILE)
target_compile_options (aubio PRIVATE ${SNDFILE_CFLAGS})
target_link_options (aubio PRIVATE ${SNDFILE_LDFLAGS})
endif ()

pkg_check_modules (SAMPLERATE samplerate>=0.0.15)
if (SAMPLERATE_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_SAMPLERATE)
target_compile_options (aubio PRIVATE ${SAMPLERATE_CFLAGS})
target_link_options (aubio PRIVATE ${SAMPLERATE_LDFLAGS})
endif ()

pkg_check_modules (RUBBERBAND rubberband>=1.3)
if (RUBBERBAND_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_RUBBERBAND)
target_compile_options (aubio PRIVATE ${RUBBERBAND_CFLAGS})
target_link_options (aubio PRIVATE ${RUBBERBAND_LDFLAGS})
endif ()

# TODO jack

pkg_check_modules (LIBAV libavcodec>=54.35.0 libavformat>=52.3.0 libavutil>=52.3.0 libswresample>=1.2.0)
if (LIBAV_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_LIBAV)
target_compile_options (aubio PRIVATE ${LIBAV_CFLAGS})
target_link_options (aubio PRIVATE ${LIBAV_LDFLAGS})
endif ()

pkg_check_modules (VORBISENC vorbisenc vorbis ogg)
if (VORBISENC_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_VORBISENC)
target_compile_options (aubio PRIVATE ${VORBISENC_CFLAGS})
target_link_options (aubio PRIVATE ${VORBISENC_LDFLAGS})
endif ()

pkg_check_modules (FLAC flac)
if (FLAC_FOUND)
target_compile_definitions (aubio PRIVATE HAVE_FLAC)
target_compile_options (aubio PRIVATE ${FLAC_CFLAGS})
target_link_options (aubio PRIVATE ${FLAC_LDFLAGS})
endif ()

# generate config.h
target_compile_definitions (aubio PRIVATE HAVE_CONFIG_H)
target_include_directories (aubio PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories (aubio PRIVATE ${CMAKE_CURRENT_BINARY_DIR})

configure_file (config.h.cmake.in config.h)

target_sources (aubio PRIVATE
    "fvec.c"
    "fmat.c"
    "cvec.c"
    "lvec.c"
    "vecutils.c"
    "mathutils.c"
    "musicutils.c"
    # io
    "io/audio_unit.c"
    "io/ioutils.c"
    "io/sink.c"
    "io/sink_apple_audio.c"
    "io/sink_flac.c"
    "io/sink_sndfile.c"
    "io/sink_vorbis.c"
    "io/sink_wavwrite.c"
    "io/source.c"
    "io/source_apple_audio.c"
    "io/source_avcodec.c"
    "io/source_sndfile.c"
    "io/source_wavread.c"
    "io/utils_apple_audio.c"
    # utils
    "utils/log.c"
    "utils/windll.c"
    "utils/hist.c"
    "utils/scale.c"
    "utils/parameter.c"
    "utils/strutils.c"
    # spectral
    "spectral/filterbank_mel.c"
    "spectral/dct_ooura.c"
    "spectral/statistics.c"
    "spectral/tss.c"
    "spectral/dct_fftw.c"
    "spectral/specdesc.c"
    "spectral/awhitening.c"
    "spectral/filterbank.c"
    "spectral/dct_accelerate.c"
    "spectral/fft.c"
    "spectral/dct.c"
    "spectral/mfcc.c"
    "spectral/phasevoc.c"
    "spectral/dct_plain.c"
    "spectral/dct_ipp.c"
    "spectral/ooura_fft8g.c"
    #temporal
    "temporal/biquad.c"
    "temporal/filter.c"
    "temporal/a_weighting.c"
    "temporal/c_weighting.c"
    "temporal/resampler.c"
    # pitch
    "pitch/pitchspecacf.c"
    "pitch/pitchschmitt.c"
    "pitch/pitchmcomb.c"
    "pitch/pitchyinfast.c"
    "pitch/pitchyin.c"
    "pitch/pitchfcomb.c"
    "pitch/pitchyinfft.c"
    "pitch/pitch.c"
    # synth
    "synth/sampler.c"
    "synth/wavetable.c"
    # notes
    "notes/notes.c"
    # tempo
    "tempo/tempo.c"
    "tempo/beattracking.c"
    # effects
    "effects/rubberband_utils.c"
    "effects/pitchshift_dummy.c"
    "effects/pitchshift_rubberband.c"
    "effects/timestretch_dummy.c"
    "effects/timestretch_rubberband.c"
    # onset
    "onset/peakpicker.c"
    "onset/onset.c"
)

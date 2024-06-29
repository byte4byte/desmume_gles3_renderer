static bool setupEGL(int w, int h) {
      // EGL config attributes
      const EGLint confAttr[] = {
              EGL_RENDERABLE_TYPE, EGL_OPENGL_ES3_BIT,
              EGL_SURFACE_TYPE, EGL_PBUFFER_BIT,
              EGL_RED_SIZE,   8,
              EGL_GREEN_SIZE, 8,
              EGL_BLUE_SIZE,  8,
              EGL_ALPHA_SIZE, 8,
              EGL_DEPTH_SIZE, 16,
              EGL_NONE
      };

      // EGL context attributes
      const EGLint ctxAttr[] = {
              EGL_CONTEXT_CLIENT_VERSION, 3, 
              EGL_NONE
      };

      // surface attributes
      // the surface size is set to the input frame size
      const EGLint surfaceAttr[] = {
              EGL_WIDTH, w,
              EGL_HEIGHT, h,
              EGL_NONE
      };

      EGLint eglMajVers, eglMinVers;
      EGLint numConfigs;

      eglDisp = eglGetDisplay(EGL_DEFAULT_DISPLAY);
      if (eglInitialize(eglDisp, &eglMajVers, &eglMinVers) == EGL_FALSE) {
        toast("eglInitialize failed");
        return false;
      }

      eglBindAPI(EGL_OPENGL_ES_API);

      // choose the first config, i.e. best config
      eglChooseConfig(eglDisp, confAttr, &eglConf, 1, &numConfigs);

      eglCtx = eglCreateContext(eglDisp, eglConf, EGL_NO_CONTEXT, ctxAttr);
      if (eglCtx == EGL_NO_CONTEXT) {
          toast("eglCreateContext failed");
          return false;
      }

      // create a pixelbuffer surface
      eglSurface = eglCreatePbufferSurface(eglDisp, eglConf, surfaceAttr);
      if (eglSurface == EGL_NO_SURFACE) {
        toast("eglCreatePbufferSurface failed");
        return false;
      }

      EGLBoolean ret = eglMakeCurrent(eglDisp, eglSurface, eglSurface, eglCtx);
      if (ret == EGL_TRUE) {
        return true;
      }
      else {
        toast("eglMakeCurrent failed");
        return false;
      }
  }

static bool android_glinit(void) { 
  return setupEGL(256, 384);
}

int main() {
  oglrender_init = android_glinit;
  ...
}

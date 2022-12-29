:py:mod:`regularizepsf.psf`
===========================

.. py:module:: regularizepsf.psf


Module Contents
---------------

.. py:class:: PointSpreadFunctionABC

   .. py:property:: parameters
      :abstractmethod:

      Varying parameters of the model.

   .. py:method:: __call__(x: Real | np.ndarary, y: Real | np.ndarray) -> Real | np.ndarray
      :abstractmethod:

      Evaluation of the point spread function

      :param x: first dimension coordinate to evaluate at
      :type x: real number or `np.ndarray`
      :param y: second dimension coordinate to evaluate at
      :type y: real number or `np.ndarray`

      :returns: the value of the point spread function at (x,y)
      :rtype: real number or `np.ndarray`



.. py:class:: SimplePSF(function: Callable)

   Bases: :py:obj:`PointSpreadFunctionABC`

   Model for a simple PSF

   Creates a PSF object

   :param function: Python function representing the PSF, first two parameters must be x and y and must return an numpy array

   .. py:property:: parameters
      :type: set[str]

      Varying parameters of the model.

   .. py:method:: __call__(x, y, **kwargs) -> Real | np.ndarray

      Evaluation of the point spread function

      :param x: first dimension coordinate to evaluate at
      :type x: real number or `np.ndarray`
      :param y: second dimension coordinate to evaluate at
      :type y: real number or `np.ndarray`

      :returns: the value of the point spread function at (x,y)
      :rtype: real number or `np.ndarray`



.. py:function:: simple_psf(arg=None) -> SimplePSF


.. py:class:: VariedPSF(vary_function: Callable, base_psf: SimplePSF, validate_at_call: bool = True)

   Bases: :py:obj:`PointSpreadFunctionABC`

   Model for a PSF that varies over the field of view

   .. py:property:: parameters

      Varying parameters of the model.

   .. py:method:: __call__(x, y)

      Evaluation of the point spread function

      :param x: first dimension coordinate to evaluate at
      :type x: real number or `np.ndarray`
      :param y: second dimension coordinate to evaluate at
      :type y: real number or `np.ndarray`

      :returns: the value of the point spread function at (x,y)
      :rtype: real number or `np.ndarray`



.. py:function:: varied_psf(base_psf: SimplePSF = None)



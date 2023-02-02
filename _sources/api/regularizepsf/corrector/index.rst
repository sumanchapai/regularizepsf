:py:mod:`regularizepsf.corrector`
=================================

.. py:module:: regularizepsf.corrector


Module Contents
---------------

.. py:class:: CorrectorABC

   .. py:method:: save(path: str | Path) -> None
      :abstractmethod:

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path: str | Path) -> CorrectorABC
      :classmethod:
      :abstractmethod:

      Loads a model from the path

      :param path: where to load the model from, suggested extension is ".psf"
      :type path: str or `pathlib.Path`


   .. py:method:: correct_image(image: numpy.ndarray, size: int, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray
      :abstractmethod:

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image, only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: simulate_observation(image: numpy.ndarray) -> numpy.ndarray
      :abstractmethod:

      Simulates on a star field what an observation using this PSF looks like

      :param image: image of point source stars to simluate PSF for
      :type image: 2D float np.ndarray

      :returns: an image with the PSF applied
      :rtype: np.ndarray



.. py:class:: FunctionalCorrector(psf: regularizepsf.psf.PointSpreadFunctionABC, target_model: SimplePSF | None)

   Bases: :py:obj:`CorrectorABC`

   A version of the PSF corrector that stores the model as a set of functions.
   For the actual correction, the functions must first be evaluated to an ArrayCorrector.

   Initialize a FunctionalCorrector

   :param psf: the model describing the psf for each patch of the image
   :type psf: SimplePSF or VariedPSF
   :param target_model: the target PSF to use to establish uniformity across the image
   :type target_model: SimplePSF or None

   .. py:property:: is_variable
      :type: bool

      returns: True if the PSF model is varied (changes across the field-of-view) and False otherwise
      :rtype: bool

   .. py:method:: evaluate_to_array_form(x: numpy.ndarray, y: numpy.ndarray, size: int) -> ArrayCorrector

      Evaluates a FunctionalCorrector to an ArrayCorrector

      :param x: the first dimension coordinates to evaluate over
      :type x: np.ndarray
      :param y: the second dimension coordinates to evaluate over
      :type y: np.ndarray
      :param size: how large the patches in the PSF correction model shouuld be
      :type size: int

      :returns: an array evaluated form of this PSF corrector
      :rtype: ArrayCorrector


   .. py:method:: correct_image(image: numpy.ndarray, size: int, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image, only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: save(path)

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path)
      :classmethod:

      Loads a model from the path

      :param path: where to load the model from, suggested extension is ".psf"
      :type path: str or `pathlib.Path`


   .. py:method:: simulate_observation(image: numpy.ndarray, size: int) -> numpy.ndarray

      Simulates on a star field what an observation using this PSF looks like

      :param image: image of point source stars to simluate PSF for
      :type image: 2D float np.ndarray
      :param size: the PSF will be evaluated to size x size pixels box
      :type size: int

      :returns: an image with the PSF applied
      :rtype: np.ndarray



.. py:class:: ArrayCorrector(evaluations: dict[Any, numpy.ndarray], target_evaluation: numpy.ndarray)

   Bases: :py:obj:`CorrectorABC`

   A PSF corrector that is evaluated as array patches


   Initialize an ArrayCorrector

   :param evaluations:
                       evaluated version of the PSF as they vary over the image, keys should be (x, y) of the lower left
                           pixel of each patch. values should be the `np.ndarray` that corresponds to that patch
   :type evaluations: dict
   :param target_evaluation: evaluated version of the target PSF
   :type target_evaluation: np.ndarray

   .. py:method:: correct_image(image: numpy.ndarray, size: int = None, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image, only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: __getitem__(xy) -> numpy.ndarray


   .. py:method:: save(path)

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path)
      :classmethod:

      Loads a model from the path

      :param path: where to load the model from, suggested extension is ".psf"
      :type path: str or `pathlib.Path`


   .. py:method:: simulate_observation(image: numpy.ndarray) -> numpy.ndarray

      Simulates on a star field what an observation using this PSF looks like

      :param image: image of point source stars to simluate PSF for
      :type image: 2D float np.ndarray

      :returns: an image with the PSF applied
      :rtype: np.ndarray



.. py:function:: calculate_covering(image_shape: tuple[int, int], size: int) -> numpy.ndarray

   Determines the grid of overlapping neighborhood patches.

   :param image_shape: shape of the image we plan to correct
   :type image_shape: tuple of 2 ints
   :param size: size of the square patches we want to create
   :type size: int

   :returns: an array of shape Nx2 where return[:, 0] are the x coordinate and return[:, 1] are the y coordinates
   :rtype: np.ndarray



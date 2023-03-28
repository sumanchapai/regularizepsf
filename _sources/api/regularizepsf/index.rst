:py:mod:`regularizepsf`
=======================

.. py:module:: regularizepsf


Submodules
----------
.. toctree::
   :titlesonly:
   :maxdepth: 1

   corrector/index.rst
   exceptions/index.rst
   fitter/index.rst
   psf/index.rst


Package Contents
----------------

.. py:class:: ArrayCorrector(evaluations: dict[Any, numpy.ndarray], target_evaluation: numpy.ndarray)

   Bases: :py:obj:`CorrectorABC`

   A PSF corrector that is evaluated as array patches


   Initialize an ArrayCorrector

   :param evaluations:
                       evaluated version of the PSF as they vary over the image,
                           keys should be (x, y) of the lower left
                           pixel of each patch. values should be the `np.ndarray`
                           that corresponds to that patch
   :type evaluations: dict
   :param target_evaluation: evaluated version of the target PSF
   :type target_evaluation: np.ndarray

   .. py:method:: correct_image(image: numpy.ndarray, size: int = None, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image,
                   only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification
                    to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: __getitem__(xy: Tuple[int, int]) -> numpy.ndarray


   .. py:method:: save(path: str) -> None

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path: str) -> ArrayCorrector
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



.. py:class:: FunctionalCorrector(psf: regularizepsf.psf.PointSpreadFunctionABC, target_model: SimplePSF | None)

   Bases: :py:obj:`CorrectorABC`

   A version of the PSF corrector that stores the model as a set of functions.
   For the actual correction, the functions must first
   be evaluated to an ArrayCorrector.

   Initialize a FunctionalCorrector

   :param psf: the model describing the psf for each patch of the image
   :type psf: SimplePSF or VariedPSF
   :param target_model: the target PSF to use to establish uniformity across the image
   :type target_model: SimplePSF or None

   .. py:property:: is_variable
      :type: bool

      



      returns: True if the PSF model is varied (changes across the field-of-view)
                and False otherwise
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
      :param size: how big to make the patches when correcting an image,
                   only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification
                    to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: save(path: str) -> None

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path: str) -> FunctionalCorrector
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



.. py:function:: calculate_covering(image_shape: tuple[int, int], size: int) -> numpy.ndarray

   Determines the grid of overlapping neighborhood patches.

   :param image_shape: shape of the image we plan to correct
   :type image_shape: tuple of 2 ints
   :param size: size of the square patches we want to create
   :type size: int

   :returns: an array of shape Nx2 where return[:, 0]
             are the x coordinate and return[:, 1] are the y coordinates
   :rtype: np.ndarray


.. py:class:: CoordinatePatchCollection(patches: dict[Any, numpy.ndarray])

   Bases: :py:obj:`PatchCollectionABC`

   A representation of a PatchCollection that operates
   on pixel coordinates from a set of images

   .. py:method:: extract(images: list[numpy.ndarray], coordinates: list[CoordinateIdentifier], size: int) -> PatchCollectionABC
      :classmethod:

      Construct a PatchCollection from a set of images
      using the specified coordinates and patch size

      :param images: the images loaded
      :type images: list of np.ndarrays
      :param coordinates:
                          A list of coordinates for the lower left pixel of each patch,
                              specified in each type of PatchCollection
      :type coordinates: list
      :param size: size of one side of the square patches extracted
      :type size: int

      :returns: the square patches extracted into a PatchCollection
      :rtype: np.ndarray


   .. py:method:: find_stars_and_average(images: list[str] | np.ndarray | Generator, psf_size: int, patch_size: int, interpolation_scale: int = 1, average_mode: str = 'median', star_threshold: int = 3, hdu_choice: int = 0) -> CoordinatePatchCollection
      :classmethod:

      Loads a series of images, finds stars in each,
          and builds a CoordinatePatchCollection with averaged stars

      :param images: The images to be processed. Can be a list of FITS filenames, a
                     numpy array of shape (n_images, ny, nx), or a Generator that yields
                     each data array in turn.
      :type images: List[str] or np.ndarray or Generator
      :param psf_size: size of the PSF model to use
      :type psf_size: int
      :param patch_size: square size that each PSF model applies to
      :type patch_size: int
      :param interpolation_scale:
                                  if >1, the image are first scaled by this factor.
                                      This results in stars being aligned at a subpixel scale
      :type interpolation_scale: int
      :param average_mode: "median" or "mean" determines how patches are combined
      :type average_mode: str
      :param star_threshold:
                             SEP's threshold for finding stars. See `threshold`
                                 in https://sep.readthedocs.io/en/v1.1.x/api/sep.extract.html#sep-extract
      :type star_threshold: int
      :param hdu_choice:
                         Which HDU from each image will be used,
                             default of 0 is most common but could be 1 for compressed images
      :type hdu_choice: int

      :returns: An averaged star model built from the provided images
      :rtype: CoordinatePatchCollection

      .. rubric:: Notes

      Using an `interpolation_scale` other than 1
          for large images can dramatically slow down the execution.


   .. py:method:: average(corners: numpy.ndarray, patch_size: int, psf_size: int, mode: str = 'median') -> PatchCollectionABC

      Construct a new PatchCollection where patches
      lying inside a new grid are averaged together

      :param corners: an Nx2 `np.ndarray` of the lower left corners of the new patch grid
      :type corners: np.ndarray
      :param step: how far apart each corner patch is
      :type step: int
      :param size: dimension of the new (size, size) shaped square patches
      :type size: int
      :param mode: either average using "mean" or "median"
      :type mode: str

      :returns: a PatchCollection where data is sampled at the new grid
      :rtype: PatchCollectionABC


   .. py:method:: fit(base_psf: regularizepsf.psf.SimplePSF, is_varied: bool = False) -> regularizepsf.psf.PointSpreadFunctionABC
      :abstractmethod:

      :param base_psf:
      :param is_varied:


   .. py:method:: to_array_corrector(target_evaluation: numpy.array) -> regularizepsf.corrector.ArrayCorrector

      Converts a patch collection that has been averaged into an ArrayCorrector

      :param target_evaluation: the evaluation of the Target PSF
      :type target_evaluation: np.ndarray

      :returns: An array corrector that can be used to correct PSFs
      :rtype: ArrayCorrector



.. py:function:: simple_psf(arg: Any = None) -> SimplePSF


.. py:function:: varied_psf(base_psf: SimplePSF = None) -> VariedPSF



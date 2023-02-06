:py:mod:`regularizepsf.fitter`
==============================

.. py:module:: regularizepsf.fitter


Module Contents
---------------

.. py:class:: PatchCollectionABC(patches: dict[Any, numpy.ndarray])

   .. py:method:: __len__()


   .. py:method:: extract(images: list[numpy.ndarray], coordinates: list, size: int) -> PatchCollectionABC
      :classmethod:
      :abstractmethod:

      Construct a PatchCollection from a set of images using the specified coordinates and patch size

      :param images: the images loaded
      :type images: list of np.ndarrays
      :param coordinates: A list of coordinates for the lower left pixel of each patch, specified in each type of PatchCollection
      :type coordinates: list
      :param size: size of one side of the square patches extracted
      :type size: int

      :returns: the square patches extracted into a PatchCollection
      :rtype: np.ndarray


   .. py:method:: __getitem__(identifier: Any) -> numpy.ndarray

      Access a patch with square brackets

      :param identifier: identifier for a given patch, specifically implemented for each PatchCollection
      :type identifier: Any

      :returns: a patch's data
      :rtype: np.ndarray


   .. py:method:: __contains__(identifier: Any) -> bool

      Determines if a patch is in the collection

      :param identifier: identifier for a given patch, specifically implemented for each PatchCollection
      :type identifier: Any

      :returns: True if patch with specified identifier is in the collection, False otherwise
      :rtype: bool


   .. py:method:: add(identifier: Any, patch: numpy.ndarray) -> None

      Add a new patch to the collection

      :param identifier: identifier for a given patch, specifically implemented for each PatchCollection
      :type identifier: Any
      :param patch: the data for a specific patch
      :type patch: np.ndarray

      :rtype: None


   .. py:method:: average(corners: numpy.ndarray, step: int, size: int, mode: str) -> PatchCollectionABC
      :abstractmethod:

      Construct a new PatchCollection where patches lying inside a new grid are averaged together

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


   .. py:method:: save(path: str) -> None

      Save the PatchCollection to a file

      :param path: where to save the patch collection
      :type path: str

      :rtype: None


   .. py:method:: load(path) -> PatchCollectionABC
      :classmethod:

      Load a PatchCollection from a file

      :param path: file path to load from
      :type path: str

      :returns: the new patch collection
      :rtype: PatchCollectionABC


   .. py:method:: keys()

      Gets identifiers for all patches


   .. py:method:: values()

      Gets values of all patches


   .. py:method:: items()

      A dictionary like iterator over the patches


   .. py:method:: __next__()



.. py:data:: CoordinateIdentifier

   

.. py:class:: CoordinatePatchCollection(patches: dict[Any, numpy.ndarray])

   Bases: :py:obj:`PatchCollectionABC`

   A representation of a PatchCollection that operates on pixel coordinates from a set of images

   .. py:method:: extract(images: list[numpy.ndarray], coordinates: list[CoordinateIdentifier], size: int) -> PatchCollectionABC
      :classmethod:

      Construct a PatchCollection from a set of images using the specified coordinates and patch size

      :param images: the images loaded
      :type images: list of np.ndarrays
      :param coordinates: A list of coordinates for the lower left pixel of each patch, specified in each type of PatchCollection
      :type coordinates: list
      :param size: size of one side of the square patches extracted
      :type size: int

      :returns: the square patches extracted into a PatchCollection
      :rtype: np.ndarray


   .. py:method:: find_stars_and_average(image_paths: list[str], psf_size: int, patch_size: int, interpolation_scale: int = 1, average_mode: str = 'median', star_threshold: int = 3, hdu_choice=0)
      :classmethod:

      Loads a series of images, finds stars in each, and builds a CoordinatePatchCollection with averaged stars

      :param image_paths: location of FITS files to load
      :type image_paths: List[str]
      :param psf_size: size of the PSF model to use
      :type psf_size: int
      :param patch_size: square size that each PSF model applies to
      :type patch_size: int
      :param interpolation_scale: if >1, the image are first scaled by this factor. This results in stars being aligned at a subpixel scale
      :type interpolation_scale: int
      :param average_mode: "median" or "mean" determines how patches are combined
      :type average_mode: str
      :param star_threshold: SEP's threshold for finding stars. See `threshold` in https://sep.readthedocs.io/en/v1.1.x/api/sep.extract.html#sep-extract
      :type star_threshold: int
      :param hdu_choice: Which HDU from each image will be used, default of 0 is most common but could be 1 for compressed images
      :type hdu_choice: int

      :returns: An averaged star model built from the provided images
      :rtype: CoordinatePatchCollection

      .. rubric:: Notes

      Using an `interpolation_scale` other than 1 for large images can dramatically slow down the execution.


   .. py:method:: average(corners: numpy.ndarray, patch_size: int, psf_size: int, mode: str = 'median') -> PatchCollectionABC

      Construct a new PatchCollection where patches lying inside a new grid are averaged together

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




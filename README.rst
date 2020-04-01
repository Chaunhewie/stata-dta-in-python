###################
Stata dta in Python
###################

Please Turn to See https://github.com/jrfiedler/stata-dta-in-python.git . As this repository is just personalized extended for cutting a large dta(formats 118) file into small files.

This repo version entends for dta formats 118 while origin repo was inplemented for dta formats 117, 115, and 114)

This is a package for using Stata .dta files in Python. The main functionality of the package is in its ``Dta`` class and subclasses, which encapsulate the information from a .dta file, and provide methods for adding, replacing, or deleting this information.

You can create ``Dta`` objects from .dta files or from iterables of Python values. You can manipulate ``Dta`` objects in basic ways (add observations, replace data values, rename data variables etc.), and you can save ``Dta`` objects to .dta files. 

This package has been tested on Python 3.1, 3.2, and 3.3. Some parts of this package do not work in Python 2. Support for Python 2 might be added at a later date.

Currently, this package supports .dta file formats 118, 117, 115, and 114.


Requirements
============

Python 3.1 - 3.6


Installation
============

Download the package, either with::

    git clone https://github.com/Chaunhewie/stata-dta-in-python.git

or by downloading a zip archive (there's a button on the right side of this page) and unzipping. 

Then, in the main folder, use::

    python setup.py install

to install.

Changelog
=========

0.3.0
-----
- personalized extended for reading dta(formats 118) file without data, and then reading data and cut into small files.
- see `stata_data_exec.ipynb` with `jupter notebook` for more using details.

0.2.0
-----

- Added quick access to data variables, as in `dta.varname_`
- Added `stata_math` module provides functions that understand missing values and quick-access data variables
- New method `quiet()` silences warnings and other 'unexpected' output
- New method `get(row, col)` for getting a single data value

See examples "Quick access to data variables" and "Math with missing values" in EXAMPLES.rst.


Example usage v0.3.0
====================

::

    >>> dta = open_dta(file_path)

    >>> vars_to_get = ['year', 'panel_id', 'id_in_source', 'id', 'name', 'legal_person', 'region','agriculture', 'industry', 'construction_industry', 'transportation_industry',  'catering', 'other', 'business_scale', 'light_and_heavy_industry',  'gross_output_constant', 'gross_output_current',  'net_total_fixed_assets', 'total_fixed_assets',  'accumulated_depreciation', 'depreciation_this_year',  'paid_in_capital', 'national_capital', 'collective_capital', 'corporate_capital', 'personal_capital', 'hkmt_capital', 'foreign_capital', 'main_business_revenue', 'main_business_cost',  'operating_profit','payable_profit', 'payroll_payable', 'welfare_payable',  'light_heavy_industry']

    >>> data = dta.get_year_data_as_list(file_path, 2009, 2015, vars_to_get)

    >>> print(len(dta._varvals))
    2950504

    >>> print(len(dta._typlist_to_save))
    35

    >>> print(dta._nvar_to_save, dta._nobs_to_save)
    35 2950504

    >>> dta._dta_obj_to_file(save_file_path)
    Saving to `$save_file_path`
    Save Success!

    >>> dta_saved = open_dta(save_file_path)

see `stata_data_exec.ipynb` with `jupter notebook`

Example usage v0.2.0
====================

::

    >>> from stata_dta import open_dta, display_diff
    
    >>> dta1 = open_dta("C:/Program Files (x86)/Stata12/auto.dta")
    (1978 Automobile Data)

    >>> dta2 = open_dta("C:/Program Files (x86)/Stata13/auto.dta")
    (1978 Automobile Data)

    >>> display_diff(dta1, dta2)
        class types differ:
            Dta115 vs Dta117
        formats differ:
            114 vs 117
        time stamps differ:
            13 Apr 2011 17:45 vs 13 Apr 2013 17:45

    >>> dta1.list("make rep weight disp", in_=range(6))
        +--------------------------------------------------+
        | make                   rep78    weight  displa~t |
        +--------------------------------------------------+
     0. | AMC Concord                3     2,930       121 |
     1. | AMC Pacer                  3     3,350       258 |
     2. | AMC Spirit                 .     2,640       121 |
     3. | Buick Century              3     3,250       196 |
     4. | Buick Electra              4     4,080       350 |
        +--------------------------------------------------+
     5. | Buick LeSabre              3     3,670       231 |
        +--------------------------------------------------+

    >>> dta1[:6, ::3].list()
        +--------------------------------------------------+
        | make                   rep78    weight  displa~t |
        +--------------------------------------------------+
     0. | AMC Concord                3     2,930       121 |
     1. | AMC Pacer                  3     3,350       258 |
     2. | AMC Spirit                 .     2,640       121 |
     3. | Buick Century              3     3,250       196 |
     4. | Buick Electra              4     4,080       350 |
        +--------------------------------------------------+
     5. | Buick LeSabre              3     3,670       231 |
        +--------------------------------------------------+

    >>> from stata_dta import Dta115, Dta117
    >>> v = [[0, 0.1, "0.2", 0.3], [1, 1.1, "1.2"], [2], [3, 3.1, 3.2, 3.3]]
    >>> for row in v:
    ...     print(row)
    ...
    [0, 0.1, '0.2', 0.3]
    [1, 1.1, '1.2']
    [2]
    [3, 3.1, 3.2, 3.3]
    
    >>> dta3 = Dta117(v)
    >>> dta2.describe()
    
      obs:             4
     vars:             4                          31 Dec 2013 17:11
     size:            80
    ----------------------------------------------------------------------
                  storage   display    value
    variable name   type    format     label      variable label
    ----------------------------------------------------------------------
    var0            byte    %8.0g
    var1            double  %10.0g
    var2            str3    %9s
    var3            double  %10.0g
    ----------------------------------------------------------------------
    Sorted by:
         Note:  dataset has changed since last saved

    >>> dta3.list()
        +---------------------------------------------+
        |     var0        var1       var2        var3 |
        +---------------------------------------------+
     0. |        0         0.1        0.2         0.3 |
     1. |        1         1.1        1.2           . |
     2. |        2           .                      . |
     3. |        3         3.1        3.2         3.3 |
        +---------------------------------------------+
    
    >>> dta3.summ()
    
        Variable |       Obs        Mean    Std. Dev.       Min        Max
    -------------+--------------------------------------------------------
            var0 |         4         1.5     1.29099          0          3
            var1 |         3     1.43333     1.52753        0.1        3.1
            var2 |         0
            var3 |         2         1.8     2.12132        0.3        3.3
    
    >>> dta3.save("example.dta")

For more examples, see EXAMPLES.md.


Contributors
============
- Chaunhewie Tian
- James Fiedler
- Matthew Koslovsky


Contact
=======
- ChaunhewieTian, chaunhewietian@163.com


License
=======
Copyright (c) 2020, Chaunhewie Tian (MIT License)
